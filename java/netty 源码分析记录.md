netty 源码查阅记录

[TOC]
#### 服务端样板代码分析

##### `EventLoopGroup `

```java
EventLoopGroup boosGroup = new NioEventLoopGroup();
EventLoopGroup workeGroup = new NioEventLoopGroup();
```

`boosGroup`负责接收客户端发过来的连接 `workeGroup`是真正处理连接

`EventLoopGroup`事件循环组，底层是死循环 。

##### Future

**`java.util.concurrent.Future`** 

用于返回异步线程执行的结果，并提供了中止和判断是否完成等方法。

**`io.netty.util.concurrent.Futrue`**

`public interface Future<V> extends java.util.concurrent.Future<V>` 对Java Future增强，主要在于Futrue的`get`方法并不知在何时调用，因为在异步任务未完成的情况下，`get`方法是阻塞的，而`netty`的Futrue里增加了`addListener`方法,通过观察中模式来避免这种现象。

##### ChannelFuture

`public interface ChannelFuture extends Future<Void>`表示异步IO`Channel`，返回关于IO操作的结果或一些状态信息，

**<u>对比</u>**

JDK所提供的`Future`只能通过手工方式检查执行结果，而这个操作是阻塞的；Netty则对`ChannelFuture`进行了增强，通过`ChannelFutureListener`以回调的方式获取执行结果，去除了手工检查阻塞的操作；值得注意的是：`ChannelFutureListener`的`operationComplete`方法是由I/O线程执行的，因此避免放超时业务操作。

#### Neety 整体架构是Reactor 模式

> Reactor 是对于传统的一个连接进来然后服务器新建一个线程的改进：
>
> - 在高并发的情况下会产生大量的线程，加大线程上下文切换开销。

**Reactor模式的角色构成(一共5种角色)**

![Reactor模式图解](/Users/bl/Documents/许些截图/Reactor_img.png)

1. `Handel（句柄或描述符）`：本质上表示一种资源，是由操作系统提供的；该资源用于表示一个个的事件，比如说文件描述符，或是针对网络编程中的Socket描述符。事件即可以来自外部，也可以来自于内部；外部事件比如说客户端的连接请求，客服端发过来数据等；内部事件比如说操作系统产生的定时器事件等。`Handle`是事件产生的发源地。
2. `SynchronousEventDemultiplexer（同步事件分离器）`：它本身是一个系统调用，用于等待特定的事件发生。调用方在调用它的时候会被阻塞，一直阻塞到同步事件分离器上有事件产生为止。对于Linux来说，同步事件分离器指的是常用的I/O多路复用，比如说`select`，`poll`	,`epoll`等。在Java NIO领域中，同步事件分离器对应的组件就是`Selector`；对应的阻塞方法就是`select`方法。
3. `EventHandler（事件处理器）`：本身有多个回调方法构成，这些回调方法构成了与应用相关的对与某个事件的反馈机制。
4. `ConcreteEventHandler（具体事件处理器）`：是事件处理器的实现。它本身实现了事件处理器所提供的各个回调方法，从而实现了特定于业务的逻辑。它本质上就是我们所编写的一个个的处理器实现。
5. `Initiation Dispatcher（初始分发器）`：实际上就是Reactor角色。它本身定义了一些规范，这些规范用于控制事件的调度方式，同时又提供了应用进行事件处理器的注册、删除等设施。它是整个事件处理器核心所在，`InitiationDispatcher`会通过同步事件分离器等待事件的发生。 

##### Reactor模式流程

1. 当应用向`Initiation Dispatcher`注册具体的事件处理器时，应用会标识出该事件处理器希望`Initiation Dispatcher`在某个事件发生时向其通知该事件，该事件与`Handle`关联。
2. `Initiation Dispatcher`会要求每个事件处理器向其传递内部的`Handle`。该`Handle`向操作系统识别了事件处理器。
3. 当所有的事件处理器注册完毕后，应用会调用`handle_vents`方法来启动`Initiation Dispatcher`的事件循环。这时，`Initiation Dispatcher`会将每个注册的事件管理器的`Hanlde`合并起来，并使用同步事件分离器等待这些事件的发生，比如说，TPC协议层会使用select同步事件分离器操作来等待客户端发送的数据到达连接的`sockethandle`上。
4. 当某个事件源对应的`Hanlde`变为`ready`状态时（比如说，TCP Socket变为待读状态时），同步事件分离器就会通知`Initiation Dispatcher`。
5. `Initiation Dispatcher`会触发事件处理器的回调方法，从而响应这个处于ready状态的`Handle`。当事件发生时，`Initiation Dispatcher`会将被事件源激活的`Handle`作为`Key`来寻找并分发恰当的事件处理器回调方法。
6. `Initiation Dispatcher`会回调事件处理器的`handle_evnets`回调方法执行特定与应用的功能（开发着自己所编写的功能），从而响应这个事件。所发生的事件类型可以作为方法参数并被该方法内部使用来执行额外的特定于服务的分离与分发。

#### netty 基本线程模型

- 一个`EventLoopGroup`当中包含一个或多个`EventLoop`。
- 一个`EventLoop`在它的整个生命周期当中都只会与唯一一个`Thread`进行绑定
- 所有由`EventLoop`所处理的各种I/O事件都在在它所关联的那个`Trhead`上进行处理。
- 一个`Channel`在它的整个生命周期中只会注册一个`EventLoop`上。
- 一个`EventLoop`在运行过程中，会被分配给一个或者多个`Channel`。

 **结论：**

 1. 在Netty中，`Channel`的实现一定是线程安全的；基于此，我们可以存储一个`Channel`引用，并且在需要向远程端点发送数据时，通过这个引用来调用`Channel`相应的方法；即便当时有很多线程都在使用它也不会出现多线程问题；而且，消息一定会按照顺序发送出去。

 2. 我们在业务开发中，不要将长时间执行的耗时任务放入到`EventLoop`的执行队列中，因为它将会一直阻塞该线程所对应的所有`Channel`上的其它执行任务，如果我们需求进行阻塞调用或是耗时操作（实际开发很常见），那么我们就需要使用一个专门的`EventExecutor`（业务线程池）。

       *<!--通常会有两种实现方式：-->*

            1. 在`ChannelHandler`的回调方法中，使用自己定义的业务线程池，这样就可以实现异步调用。
             2. 借助于Neety提供的向`ChannelPipeline`添加`ChannelHandler`时调用的`addLast`方法来传递`EventExecutor`。

> 默认情况下（调用`addLast(handler)`），`ChannelHandler`中的回调方法都是由I/O线程执行，如果调用了`addLast(EventExecutorGroup group,...)`方法，那么`ChannelHandler`中的回调方法是有`group`线程组来执行。

#### Channel两种发送消息方式

`Channel`和`ChannelHandlerContext` 区别：

> 在Netty中有两种发消息的方式，可以直接写到`Channel`中，也可以写到与`ChannelHandler`所关联的那个`ChannelHandlerContext`中。对于前一种方式来说，消息会从`ChannelPipeline`的末尾开始流动；对于后一种方式来说，消息将从`ChannelPipleline`中的下一个`ChannelHandler`开始流动。

**结论：**

1. `ChannelHandlerContext`与`ChannelHandler`之间的关联绑定关系是永远都不会发生改变的，因此对其进行缓存是没有任何问题的。
2. 对于与`Channle`的同名方法来说，`ChannelHandlerContext`的方法将会产生更短的事件流，所以我们应该在可能的情况下利用这个特性来提升应用性能。

>场景：当Netty程序既做`Server`又需要做`Client` 的时候，最佳实践为，这个程序服务连接的`Channel`和客户连接的`Channel`对象共用一个`EventLoop`。

#### Netty ByteBuf

***Netty ByteBuf所提供3种缓冲区类型：***

```java
1.heap buffer. 堆内缓冲区
2.direct buffer.直接缓冲区，操作系统在本地内存分配，JVM引用指向此内存地址
3.composite buffer. 复合缓冲区(buffer 容器)
```

##### Heap Buffer(堆缓冲区)

**说明：** 这是最常用的类型，`ByteBuf`将数据储存到JVM堆空间中，并且将实际数据存放到`byte array`中实现。

**优点：** 由于数据存储在JVM堆中，因此可以快速的创建和销毁对象，并且它提供了直接访问内部字节数组的方法。

**缺点：**每次读写数据是，都需要先将数据复制到直接缓冲区中再进行网络传输。

##### Direct Buffer（直接缓冲区）

**说明：** 在堆外直接分配内存空间，直接缓冲区并不会占用堆的容量空间，因为它是由操作系统在本地内存进行数据分配。

**优点：** 在`Socket`	数据传递时，性能非常好，不需数据从JVM复制到本地缓冲操作。

**缺点：**因为`Direct Buufer`是直接在操作系统内存中的，所以内存空间的分配与释放比堆空间更加复杂；*Neety 通过提供`内存池`来解决这个问题*；直接缓冲区并`不支持`通过数组方式访问数据。
> **重点：**对于后端的业务消息的解码来说，推荐使用`HeapByteBuf`；对于I/O通信线程在读写缓冲区时，推荐使用`DierctByteBuf`。

##### Composite Buffer（复合缓冲区）

```java
CompositeByteBuf compositeByteBuf = Unpooled.compositeBuffer();

ByteBuf heapBuf = Unpooled.buffer(10);
ByteBuf directBuf = Unpooled.directBuffer(8);
compositeByteBuf.addComponents(heapBuf, directBuf);

// compositeByteBuf.removeComponent(0);
compositeByteBuf.forEach(System.out::println);
```

***JDK的ByteBuffer与Neety的ByteBuf之间的差异对比：***

1. Netty的ByteBuf采用了`读写索引分离`的策略（readerIdex与writerIndex)，一个初始化（里面尚未有任何数据）的ByteBuf的readerIndex与writerIndex的值都为0。
2. 当读索引与写索引处于同一个位置时，如果我们继续读取，那么就会抛出`IndexOutOfBoundsException`。
3. 对于ByteBuf的任何读写操作都会分别单独维护读索引与写索引。

**JDK的ByteBuffer的缺点：**

1. `final byte[] hb;`这是JDK的ByteBuffer对象中用于存储数据的对象声明；其字节数组是被声明为final的，也就是长度固定不变的。一旦分配好不能动态扩容与收缩；当数据大有可能出现`IndexOutOfBoundsException`。如果想要扩容，只有创建一个新的ByteBuffer对象，如何执行数据拷贝动作。

2. ByteBuffer只使用一个`position`指针来标示位置信息，在进行读写切换时就需要调用`flip`方法或是`rewind`方法。

#### AtomicIntegerFieldUpdate

*`AtomicIntegerFieldUpdate`要点总结：*

- 更新器更新的必须是`int`类型变量，不能是其它包装类型。
- 更新器更新的必须是`volatile`类型变量，确保线程之间共享变量时的立即可见性。
- 变量不能是`static`的，必须要是实例变量。因为`Unsafe.objectFieldOffset()`方法不支持静态变量（CAS操作本质是通过对象实例的偏移量来直接进行的赋值）。
- 更新容器只能修改可见范围的变量，因为更新是通过反射得到这个变量

> 如果要更新的是包装类型，那么可以使用`AtomicReferenceFieldUpdater`来进行更新。

#### Netty 处理器

```javascript
Netty的处理器可以分为两类：入站处理器与出站处理器。
```

- 入站处理器顶层是`ChannelInboundHandler`,出站处理器顶层是`ChannelOutboundHandler`。

- 数据处理时常用的各种编解码器本质都是处理器。

- 编解码器：无论我们向网络中写入的数据是什么类型（int,char,String,二进制），数据在网络中传递时，其都是以`字节流`的形式呈现的；

  - `编码` ：将数据转换字节流操作为编码(encode)；本质上是一种出站处理器。
  - `解码`：将数据由字节转换原本的格式为解码(decode)；本质上一种入站处理器。

*Netty编解码器的重要结论：*

1. 无论是编码器还是解码器，其所接收的消息类型必须要与待处理的参数类型一致，否则该编码器或解码器并不会被执行。

2. 在解码器进行数据解码时，一定要（继承的是`ByteToMessageDecoder`）记得判断缓冲(ByteBuf)中的数据是否足够。






