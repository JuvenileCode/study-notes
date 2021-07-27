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

#### 查询插件

```bash
# 查询已经安装的插件
###
GET http://192.168.1.50:9200/_cat/plugins
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
###
```

#### 查看Mapping

```bash
###
GET http://192.168.1.50:9200/index_name/_mapping
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
###
```

#### 分词器测试

```bash
### 
POST http://192.168.1.50:9200/_analyze
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
content-type: application/json; charset=UTF-8
###
# ES默认分词器standard
{"analyzer":"standard","text":"In 2020, Java is the best language in the world."}

# IK分词器ik_max_word模式(会将文本做最细粒度的拆分)
{"analyzer":"ik_max_word","text":"关于印发《北京市房屋建筑和市政基础设施工程劳务分包合同管理暂行办法》的通知"}
# IK分词器ik_smart模式(做最粗粒度的拆分)
{"analyzer":"ik_smart","text":"关于印发《北京市房屋建筑和市政基础设施工程劳务分包合同管理暂行办法》的通知"}
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

#### 索引普通查询

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

# 排序分页查询
{"query":{"match_all":{}},"sort":[{"attributes.issue_date":"desc"}],"from":0,"size":15}

# 返回指定的字段简单写法
{"query":{"match_all":{}},"_source":["id","title","attachment.content","attributes.policy_title"]}
# 返回指定的字段或排除某些字段不返回（include包含字段  exclude排除字段）
{"_source":{"include":["obj1.*","obj2.*"],"exclude":["*.description"]},"query":{"match_all":{}},"from":0,"size":30}

# term查询(完全匹配),最好设置为not_analyzed
{"query":{"term":{"id":"12479"}}}

# terms查询,多词条(terms)查询
{"query":{"terms":{"id":["12479","12480"]}}}

# match全文查询
{"query":{"match":{"full_text":"Quick Foxes"}}}
# operator：用来控制match查询匹配词条的逻辑条件，默认值是or，如果设置为and，表示查询满足所有条件
# minimum_should_match：当operator参数设置为or时，该参数用来控制应该匹配的分词的最少数量
{"from":10,"size":5,"query":{"match":{"eventname":{"query":"azure aws cloud security","operator":"or","minimum_should_match":2}}}}

# 短语匹配查询(match_phrase)
# 分析后的文本中构建短语查询，必须匹配短语中的所有分词，并且保证各个分词的相对位置不变
{"from":1,"size":100,"query":{"match_phrase":{"eventname":"Open Source"}}}

# 多字段匹配查询
{"multi_match":{"query":"Microsoft Azure","fields":["subject","message"]}}

###
```

### Es Mapping

[参考](https://www.cnblogs.com/wupeixuan/p/12514843.html)

**Mapping 类似于数据库中的表结构定义 `schema`，作用：** 

- 定义索引中的字段的名称
- 定义字段的数据类型，比如字符串、数字、布尔
- 字段，倒排索引的相关配置，比如设置某个字段为不被索引、记录 position 等

**在 ES 早期版本，一个索引下是可以有多个 Type 的，从 7.0 开始，一个索引只有一个 Type，也可以说一个 Type 有一个 Mapping 定义**

#### ES字段类型

ES 字段类型主要有：核心类型、复杂类型、地理类型以及特殊类型
![es-data-type-img](https://raw.githubusercontent.com/JuvenileCode/study-notes/master/image-source/ES_Data_Type.png)

#### 字符串类型

在 ES 7.x 有两种字符串类型：`text` 和 `keyword`，在 ES 5.x 之后 `string` 类型已经不再支持了

`text` 类型适用于需要被全文检索的字段，例如新闻正文、邮件内容等比较长的文字，`text` 类型会被 Lucene 分词器（Analyzer）处理为一个个词项，并使用 Lucene 倒排索引存储，**text 字段不能被用于排序**，如果需要使用该类型的字段只需要在定义映射时指定 JSON 中对应字段的 `type` 为 `text`

`keyword` 适合简短、结构化字符串，例如主机名、姓名、商品名称等，**可以用于过滤、排序、聚合检索，也可以用于精确查询**

#### 数字类型

数字类型分为 `long、integer、short、byte、double、float、half_float、scaled_float`。

数字类型的字段在满足需求的前提下应当尽量选择范围较小的数据类型，字段长度越短，搜索效率越高，对于浮点数，可以优先考虑使用 `scaled_float` 类型，该类型可以通过缩放因子来精确浮点数，例如 12.34 可以转换为 1234 来存储。

#### 日期类型

在 ES 中日期可以为以下形式：

- 格式化的日期字符串，例如 2020-03-17 00:00、2020/03/17
- 时间戳（和 1970-01-01 00:00:00 UTC 的差值），单位毫秒或者秒

**即使是格式化的日期字符串，ES 底层依然采用的是时间戳的形式存储。**

#### 布尔类型

JSON 文档中同样存在布尔类型，不过 JSON 字符串类型也可以被 ES 转换为布尔类型存储，前提是字符串的取值为 `true` 或者 `false`，布尔类型常用于检索中的过滤条件。

#### object （对象类型）

JSON 字符串允许嵌套对象，一个文档可以嵌套多个、多层对象。可以通过对象类型来存储二级文档，不过由于 Lucene 并没有内部对象的概念，ES 会将原 JSON 文档扁平化，例如文档：

```json
Copy{
	"name": {
		"first": "wu",
		"last": "px"
	}
}
```

实际上 ES 会将其转换为以下格式，并通过 Lucene 存储，即使 `name` 是 `object` 类型：

```json
Copy{
	"name.first": "wu",
	"name.last": "px"
}
```

#### nested（嵌套类型）

嵌套类型可以看成是一个特殊的对象类型，可以让对象数组独立检索，例如文档

嵌套类型可以看成是一个特殊的对象类型，可以让对象数组独立检索，例如文档：

```json
Copy{
  "group": "users",
  "username": [
	{ "first": "wu", "last": "px"},
	{ "first": "hu", "last": "xy"},
	{ "first": "wu", "last": "mx"}
  ]
}
```

`username` 字段是一个 JSON 数组，并且每个数组对象都是一个 JSON 对象。如果将 `username` 设置为对象类型，那么 ES 会将其转换为：

```json
Copy{
  "group": "users",
  "username.first": ["wu", "hu", "wu"],
  "username.last": ["px", "xy", "mx"]
}
```

可以看出转换后的 JSON 文档中 `first` 和 `last` 的关联丢失了，如果尝试搜索 `first` 为 `wu`，`last` 为 `xy` 的文档，那么成功会检索出上述文档，但是 `wu` 和 `xy` 在原 JSON 文档中并不属于同一个 JSON 对象，应当是不匹配的，即检索不出任何结果。

嵌套类型就是为了解决这种问题的，嵌套类型将数组中的每个 JSON 对象作为独立的隐藏文档来存储，每个嵌套的对象都能够独立地被搜索，所以上述案例中虽然表面上只有 1 个文档，但实际上是存储了 4 个文档。

#### Dynamic Mapping

Dynamic Mapping 机制使我们不需要手动定义 Mapping，ES 会**自动根据文档信息来判断字段合适的类型**，但是有时候也会推算的不对，比如地理位置信息有可能会判断为 `Text`，当类型如果设置不对时，会导致一些功能无法正常工作，比如 Range 查询。

### JAVA Api

