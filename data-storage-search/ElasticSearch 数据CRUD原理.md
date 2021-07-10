## ElasticSearch 数据CRUD原理

[参考源](https://blog.csdn.net/Agly_Clarlie/article/details/116105998)

### 查询

>**查询数据分 Query - Fetch 两阶段**
>
>**Query**
>
>- 请求打到ES节点，收到请求的节点会以`Coordinating(协调的节点)`在全部主副分片(shard)中随机选择分片发送查询请求
>- 被选中的分片执行查询排序，然后每个分⽚都会返回 排序后的⽂档 Id 和排序值给 Coordinating 节点
>
>**Fetch**
>
>- Coordinating 节点会将 Query 阶段获取的⽂档 Id 列表，重新进⾏排序。选取 From 到 From + Size 个⽂档的 Id
>- 再次发出请求到相应的分⽚获取详细的⽂档数据
>
>**读数据过程**
>
>- 可以通过 doc id 来查询，根据路由算法，默认会根据 doc id 进行 hash，判断出来当时把 doc id 分配到了对应的 shard 上面去，然后去那个 shard 去查询。
>
>- 客户端发送请求到任意一个 node，成为 coordinate node 。
>  coordinate node 对 doc id 进行哈希路由，将请求转发到对应的 node，此时会使用 round-robin 随机轮询算法，在 primary shard 以及其所有 replica 中随机选择一个，让读请求负载均衡。
>- 接收请求的 node 返回 document 给 coordinate node 。
>- coordinate node 返回 document 给客户端。

### 写入

> **写请求是写入 `primary shard`，然后同步给所有的 `replica shard`；读请求可以从 primary shard 或 replica shard 读取，采用的是随机轮询算法。**
>
> - 客户端选择一个 node 发送请求过去，这个 node 就是 coordinating node （协调节点）
> - coordinating node 对 document 进行路由，将请求转发给对应的 node（有 primary shard）
> - 实际的 node 上的 primary shard 处理请求，然后将数据同步到 replica node 
> - coordinating node 如果发现 primary node 和所有 replica node 都搞定之后，就返回响应结果给客户端
>
> **写数据过程底层原理**
>
> 1. 先写入内存 `buffer`，在 buffer 里的时候数据是搜索不到的；同时将数据写入 `translog` 日志文件。
>
> 2. 如果 buffer 快满了，或者到一定时间，就会将内存 buffer 数据 refresh 到一个新的 `segment file `中(存在os cache)，但是此时数据不是直接进入 segment file 磁盘文件，而是先进入 os cache 。这个过程就是 refresh 。(生成的segment file 存在os cache)(此时构建倒排索引)
>
> 3. 每隔 1 秒钟，es 将 buffer 中的数据写入一个新的 segment file ，每秒钟会产生一个新的磁盘文件 segment file ，这个 segment file 中就存储最近 1 秒内 buffer 中写入的数据。(生成的segment file 存在os cache)；但是如果 buffer 里面此时没有数据，那当然不会执行 refresh 操作，如果 buffer 里面有数据，默认 1 秒钟执行一次 refresh 操作，刷入一个新的 segment file 中。
>
> 4. 操作系统里面，磁盘文件其实都有一个东西，叫做 `os cache `，即操作系统缓存，就是说数据写入磁盘文件之前，会先进入 os cache ，先进入操作系统级别的一个内存缓存中去。只要 buffer 中的数据被 refresh 操作刷入 os cache 中，这个数据就可以被搜索到了。(所以es是NRT特性)
>
>    ​	为什么叫 es 是准实时的？ NRT ，全称` near real-time` 。默认是每隔 1 秒 refresh 一次的，所以 es 是准实时的，因为写入的数据 1 秒之后才能被看到。可以通过 es 的 restful api 或者 java api ，手动执行一次 refresh 操作，就是手动将 buffer 中的数据刷入 os cache 中，让数据立马就可以被搜索到。只要数据被输入 os cache 中，buffer 就会被清空了，因为不需要保留 buffer 了，数据在 translog 里面已经持久化到磁盘去一份了。（translog 默认5s 从os cache 落地到磁盘）
>
>    ​	重复上面的步骤，新的数据不断进入 buffer 和 translog，不断将 buffer 数据写入一个又一个新的 segment file 中去，每次 refresh 完 buffer 清空，translog 保留。随着这个过程推进，translog 会变得越来越大。当 translog 达到一定长度的时候，就会触发 commit 操作。
>
> 5. commit 操作发生第一步，就是将 buffer 中现有数据 refresh 到 os cache 中去，清空 buffer。然后，将一个 commit point 写入磁盘文件，里面标识着这个 commit point 对应的所有 segment file ，同时强行将 os cache 中目前所有的数据都 fsync 到磁盘文件中去。最后清空 现有 translog 日志文件，重启一个 translog，此时 commit 操作完成。
>
>    ​	这个 commit 操作叫做 flush 。默认 30 分钟自动执行一次 flush ，但如果 translog 过大，也会触发 flush 。flush 操作就对应着 commit 的全过程，我们可以通过 es api，手动执行 flush 操作，手动将 os cache 中的数据 fsync 强刷到磁盘上去。
>
>    ​	translog 日志文件的作用是什么？你执行 commit 操作之前，数据要么是停留在 buffer 中，要么是停留在 os cache 中，无论是 buffer 还是 os cache 都是内存，一旦这台机器死了，内存中的数据就全丢了。所以需要将数据对应的操作写入一个专门的日志文件 translog 中，一旦此时机器宕机，再次重启的时候，es 会自动读取 translog 日志文件中的数据，恢复到内存 buffer 和 os cache 中去。
>
>    ​	translog 其实也是先写入 os cache 的，默认每隔 5 秒刷一次到磁盘中去，所以默认情况下，可能有 5 秒的数据会仅仅停留在 buffer 或者 translog 文件的 os cache 中，如果此时机器挂了，会丢失 5 秒钟的数据。但是这样性能比较好，最多丢 5 秒的数据。也可以将 translog 设置成每次写操作必须是直接 fsync 到磁盘，但是性能会差很多。
>
>    其实 es 第一是准实时的，数据写入 1 秒后可以搜索到；可能会丢失数据的。有 5 秒的数据，停留在 buffer、translog os cache、segment file os cache 中，而不在磁盘上，此时如果宕机，会导致 5 秒的数据丢失。
>
>**数据写入 segment file 之后(从buffer 刷新到os cache)，同时就建立好了倒排索引。**
>
>`总结一下，数据先写入内存 buffer，然后每隔 1s，将数据 refresh 到 os cache，到了 os cache 数据就能被搜索到（所以我们才说 es 从写入到能被搜索到，中间有 1s 的延迟）。每隔 5s，将数据写入 translog 文件（这样如果机器宕机，内存数据全没，最多会有 5s 的数据丢失），translog 大到一定程度，或者默认每隔 30mins，会触发 commit 操作，将缓冲区的数据都 flush 到 segment file 磁盘文件中。`
>
>    

### 更新

> 如果是更新操作，就是将原来的 doc 标识为 deleted 状态，然后新写入一条数据。

### 删除

> 如果是删除操作，commit 的时候会生成一个 .del 文件，里面将某个 doc 标识为 deleted 状态，那么搜索的时候根据 .del 文件就知道这个 doc 是否被删除了。
