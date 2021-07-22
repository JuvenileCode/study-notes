## ElasticSearch 暇记

### Http Api 

[参考](https://zq99299.github.io/note-book/elasticsearch-core/quick-start-texample/07-search.html#query-string-search)

#### 查询集群的健康状况

```bash
###
GET http://192.168.1.50:9200/_cat/health?v
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
###
# https://blog.csdn.net/weixin_44723434/article/details/90452083
1.cluster ，集群名称
2.status，集群状态 green代表健康；yellow代表分配了所有主分片，但至少缺少一个副本，此时集群数据仍旧完整；red代表部分主分片不可用，可能已经丢失数据。
3.node.total，代表在线的节点总数量
4.node.data，代表在线的数据节点的数量
5.shards， active_shards 存活的分片数量
6.pri，active_primary_shards 存活的主分片数量 正常情况下 shards的数量是pri的两倍。
7.relo， relocating_shards 迁移中的分片数量，正常情况为 0
8.init， initializing_shards 初始化中的分片数量 正常情况为 0
9.unassign， unassigned_shards 未分配的分片 正常情况为 0
10.pending_tasks，准备中的任务，任务指迁移分片等 正常情况为 0
11.max_task_wait_time，任务最长等待时间
12.active_shards_percent，正常分片百分比 正常情况为 100%
```

#### 查询所有索引

```bash
# 查询ES存在的所有索引
###
GET http://192.168.1.50:9200/_cat/indices?v
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
###
```

#### 删除某个索引

```bash
# 删除某个索引
###
DELETE http://192.168.1.50:9200/kibana_sample_data_ecommerce
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
###
```

#### 索引查询数据

```bash
# 查询索引下的数据
# url/index/type/_search?pretty=true || url/index/_search?pretty=true
###
GET http://192.168.1.50:9200/policy_store_policy_file_1626918596972/_doc/_search?pretty=true
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
###
```

**注意：**

-  [为何去掉type?](https://www.cnblogs.com/haolb123/p/14078073.html)Elasticsearch7.X移除index下的type 
-  一个index只存储一种类型的“记录”

```bash
# 查询参数记录
###
POST http://192.168.1.50:9200/policy_store_policy_file_1626918596972/_doc/_search
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
content-type: application/json; charset=UTF-8

# 分页查询
{"query":{"match_all":{}},"from":1,"size":5}

###


```

### Es Mapping



### JAVA Api

