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

自定义Mapping模板

```json
{
    "properties": {
        "id": {
            "type": "keyword",
            "ignore_above": 64
        },
        "title": {
            "type": "text",
            "analyzer": "ik_max_word"
        },
        "issue_date": {
            "type": "date",
            "format": "yyyy-MM-dd"
        },
        "source_type": {
            "type": "byte"
        },
        "file": {
            "type": "object",
            "properties": {
                "file_name": {
                    "type": "text"
                },
                "file_ext": {
                    "type": "keyword"
                }
            }
        }
    }
```

查看索引的mapping

```bash
###
GET http://192.168.1.50:9200/index_name/_mapping
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
###
```

不能修改一个已经存在的字段的[mapping](https://www.cnblogs.com/z00377750/p/13308436.html)或类型，如果确实需要修改字段映射，需要重新建立索引并使用[reindex](https://www.cnblogs.com/Ace-suiyuan008/p/9985249.html) 迁移文档数据到新索引。

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

`Java High Level REST Client`以下简称High Level）是ES官方推荐的JAVA客户端，具有即时同步ES服务器版本，功能特性齐全等特点；所以，代码基于HighLevel：

[参考一](https://www.cnblogs.com/z00377750/p/13300196.html)

[参考二](https://www.cnblogs.com/z00377750/p/13372554.html)

#### maven导入

```xml
<!-- High Level版本建议与ES版本一致 elasticsearch operate jar-->
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.8.1</version>
</dependency>
```

#### 连接配置

```java
import cn.hutool.core.util.StrUtil;
import org.apache.http.HttpHost;
import org.apache.http.auth.AuthScope;
import org.apache.http.auth.UsernamePasswordCredentials;
import org.apache.http.client.CredentialsProvider;
import org.apache.http.impl.client.BasicCredentialsProvider;
import org.elasticsearch.client.RestClient;
import org.elasticsearch.client.RestClientBuilder;
import org.elasticsearch.client.RestHighLevelClient;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import java.util.List;
/**
 * @author: Bin.L
 * @date: 2021/7/16 0016 18:03
 * @Description:
 */
@Configuration
public class ElasticSearchConfig {

    @Value("192.168.1.50:9200")
    private List<String> uris;

    @Value("elastic")
    private String userName;

    @Value("123456")
    private String password;

    @Bean
    public RestHighLevelClient restHighLevelClient() {
        HttpHost[] httpHosts = createHosts();
        RestClientBuilder restClientBuilder = RestClient.builder(httpHosts)
                .setHttpClientConfigCallback(httpClientBuilder -> {
                    CredentialsProvider credentialsProvider = new BasicCredentialsProvider();
                    credentialsProvider.setCredentials(AuthScope.ANY, new UsernamePasswordCredentials(userName, password));
                    return httpClientBuilder.setDefaultCredentialsProvider(credentialsProvider);
                });
        RestHighLevelClient restHighLevelClient = new RestHighLevelClient(restClientBuilder);
        return restHighLevelClient;
    }

    private HttpHost[] createHosts() {
        HttpHost[] httpHosts = new HttpHost[uris.size()];
        for (int i = 0; i < uris.size(); i++) {
            String hostStr = uris.get(i);
            String[] host = hostStr.split(":");
            httpHosts[i] = new HttpHost(StrUtil.trim(host[0]), Integer.valueOf(StrUtil.trim(host[1])));
        }
        return httpHosts;
    }
}
```

#### 索引基本操作

```java
import cn.hutool.core.util.StrUtil;
import cn.hutool.json.JSONUtil;
import lombok.extern.slf4j.Slf4j;
import org.elasticsearch.action.admin.indices.delete.DeleteIndexRequest;
import org.elasticsearch.action.delete.DeleteRequest;
import org.elasticsearch.action.delete.DeleteResponse;
import org.elasticsearch.action.get.GetRequest;
import org.elasticsearch.action.get.GetResponse;
import org.elasticsearch.action.index.IndexRequest;
import org.elasticsearch.action.index.IndexResponse;
import org.elasticsearch.action.support.WriteRequest;
import org.elasticsearch.action.support.master.AcknowledgedResponse;
import org.elasticsearch.client.RequestOptions;
import org.elasticsearch.client.RestHighLevelClient;
import org.elasticsearch.client.indices.CreateIndexRequest;
import org.elasticsearch.client.indices.GetIndexRequest;
import org.elasticsearch.common.settings.Settings;
import org.elasticsearch.common.unit.TimeValue;
import org.elasticsearch.common.xcontent.XContentType;
import org.elasticsearch.rest.RestStatus;
import org.elasticsearch.search.fetch.subphase.FetchSourceContext;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;
import java.io.IOException;

/**
 * @author: Bin.L
 * @date: 2021/7/16 0016 18:07
 * @Description:
 */

@Service
@Slf4j
public class ElasticSearchService {

    @Resource
    RestHighLevelClient restHighLevelClient;

    /**
     * 根据Mapping创建索引
     *
     * @param indexName
     * @param mapping
     * @return
     */
    public boolean createIndex(String indexName, String mapping) {
        GetIndexRequest getIndexRequest = new GetIndexRequest(indexName);
        try {
            boolean exists = restHighLevelClient.indices().exists(getIndexRequest, RequestOptions.DEFAULT);
            if (!exists) {
                CreateIndexRequest createIndexRequest = new CreateIndexRequest(indexName);
                Settings settings = Settings.builder()
                        .put("index.number_of_shards", 1)
                        .put("index.number_of_replicas", 1)
                        .build();

                createIndexRequest.settings(settings).mapping(mapping, XContentType.JSON);
                restHighLevelClient.indices().create(createIndexRequest, RequestOptions.DEFAULT);
            }
        } catch (Exception e) {
            log.error("create index mapping error: {}", e.getMessage());
            e.printStackTrace();
        }
        return true;
    }

    /**
     * 删除索引
     *
     * @param indexName
     * @return
     */
    public boolean deleteIndex(String indexName) {
        DeleteIndexRequest request = new DeleteIndexRequest(indexName);
        try {
            AcknowledgedResponse delete = restHighLevelClient.indices().delete(request, RequestOptions.DEFAULT);
            return delete.isAcknowledged();
        } catch (Exception e) {
            log.error("delete es index error: {}", e.getMessage());
            e.printStackTrace();
        }
        return false;
    }

    /**
     * 添加索引下的文档（es7.0版本去掉type)
     *
     * @param indexName
     * @param docJson
     * @return
     */
    public boolean insertOne(String indexName, String docJson, String id) {
        IndexRequest request = new IndexRequest(indexName);
        request.timeout(TimeValue.timeValueSeconds(1));
        request.setRefreshPolicy(WriteRequest.RefreshPolicy.WAIT_UNTIL);
        if (StrUtil.isNotBlank(id)) {
            request.id(id);
        }
        request.source(docJson, XContentType.JSON);
        try {
            IndexResponse response = restHighLevelClient.index(request, RequestOptions.DEFAULT);
            log.info("index add doc Response: {}", response.toString());
            if (response.status().getStatus() == RestStatus.CREATED.getStatus()) {
                return true;
            }
        } catch (Exception e) {
            log.error("index[{}] add doc error: {}", indexName, e.getMessage());
            e.printStackTrace();
        }

        return false;
    }

    /**
     * 判断文档是否存在
     *
     * @param indexName
     * @param id
     */
    public void isExistsDoc(String indexName, String id) {
        GetRequest getRequest = new GetRequest(indexName, id);
        getRequest.fetchSourceContext(new FetchSourceContext(false));
        getRequest.storedFields("_none_");
        try {
            boolean exists = restHighLevelClient.exists(getRequest, RequestOptions.DEFAULT);
            System.out.println(exists);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取文档内容
     *
     * @param indexName
     * @param id
     */
    public <T> T getIdDoc(String indexName, String id, Class<T> beanClass) {
        GetRequest getRequest = new GetRequest(indexName, id);
        try {
            GetResponse getResponse = restHighLevelClient.get(getRequest, RequestOptions.DEFAULT);
            T object = JSONUtil.toBean(getResponse.getSourceAsString(), beanClass);
            System.out.println(getResponse.getSourceAsString());
            System.out.println(getResponse.getId());
            return object;
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 删除某个文档
     *
     * @param indexName
     * @param id
     * @return
     */
    public boolean deleteDocId(String indexName, String id) {
        DeleteRequest request = new DeleteRequest(indexName, id);
        request.timeout(TimeValue.timeValueSeconds(1));
        request.setRefreshPolicy(WriteRequest.RefreshPolicy.WAIT_UNTIL);
        try {
            DeleteResponse deleteResponse = restHighLevelClient.delete(request, RequestOptions.DEFAULT);
            if (deleteResponse.status().getStatus() == RestStatus.OK.getStatus()) {
                return true;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return false;
    }
    
    /**
     * 批量保存文档
     *
     * @param indexName
     * @param list
     */
    public void batchInsertDoc(String indexName, List<EsPolicyPO> list) {
        BulkRequest bulkRequest = new BulkRequest();
        bulkRequest.timeout(TimeValue.timeValueSeconds(3));
        bulkRequest.setRefreshPolicy(WriteRequest.RefreshPolicy.WAIT_UNTIL);
        for (EsPolicyPO po : list) {
            bulkRequest.add(new IndexRequest(indexName).id(po.getId()).source(JSONUtil.toJsonStr(po), XContentType.JSON));
        }
        try {
            BulkResponse bulkResponse = restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT);
            // 检查是否有失败 至少有一个执行失败时，返回true
            if (bulkResponse.hasFailures()) {
                log.error("Batch Save Doc is Error ...");
            }
            // 迭代所有操作的结果
            for (BulkItemResponse bulkItemResponse : bulkResponse) {
                // 判断操作是否失败
                if (bulkItemResponse.isFailed()) {
                    // 如果失败，则获取失败信息
                    BulkItemResponse.Failure failure = bulkItemResponse.getFailure();
                    log.error("ERROR: {}", JSONUtil.toJsonStr(failure));
                    // 其它业务处理
                    continue;
                }
                // 检索操作的响应（成功与否），可以是IndexResponse、UpdateResponse或DeleteResponse，都可以看作是DocWriteResponse实例
                DocWriteResponse itemResponse = bulkItemResponse.getResponse();
                switch (bulkItemResponse.getOpType()) {
                    case INDEX:    // 处理索引操作的响应
                    case CREATE:
                        IndexResponse indexResponse = (IndexResponse) itemResponse;
                        break;
                    case UPDATE:   // 处理更新操作的响应
                        UpdateResponse updateResponse = (UpdateResponse) itemResponse;
                        break;
                    case DELETE:   // 处理删除操作的响应
                        DeleteResponse deleteResponse = (DeleteResponse) itemResponse;
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```

**测试代码：**

```java
@SpringBootTest
class ElasticSearchServiceTest {
    public static final String POLICY = "policy";
    
    @Autowired
    ElasticSearchService elasticSearchService;

    @Test
    public void createIndex() {
        // 自己定义mapping
        String mapping = "{\"properties\":{\"id\":{\"type\":\"keyword\",\"ignore_above\":64},\"title\":{\"type\":\"text\",\"analyzer\":\"ik_max_word\"},\"issue_date\":{\"type\":\"date\",\"format\":\"yyyy-MM-dd\"},\"source_type\":{\"type\":\"byte\"},\"file\":{\"type\":\"object\",\"properties\":{\"file_name\":{\"type\":\"text\"},\"file_ext\":{\"type\":\"keyword\"}}}}}";
        System.out.println(elasticSearchService.createIndex(POLICY, mapping));
    }

    @Test
    public void deleteIndex() {
        System.out.println(elasticSearchService.deleteIndex(POLICY));
    }

    @Test
    public void insertOne() {
        EsPolicyPO esPolicyPO = new EsPolicyPO();
        esPolicyPO.setId("__0V-QO89Eeu5bqoNtw7SfQ");
        esPolicyPO.setTitle("关于发布北京市地方标准《绿色建筑评价标准》的通知");
        esPolicyPO.setIssueDate(LocalDate.now());
        esPolicyPO.setSourceType(1);
        esPolicyPO.setFile(new EsPolicyPO.File("关于发布北京市地方标准《绿色建筑评价标准》的通知.html", "html"));
        System.out.println(elasticSearchService.insertOne(POLICY, JSONUtil.toJsonPrettyStr(esPolicyPO), esPolicyPO.getId()));
    }

    @Test
    public void isExists() {
        elasticSearchService.isExistsDoc(POLICY, "__0V-QO89Eeu5bqoNtw7SfQ");
    }

    @Test
    public void getIdDoc() {
        System.out.println(elasticSearchService.getIdDoc(POLICY, "__0V-QO89Eeu5bqoNtw7SfQ", EsPolicyPO.class));
    }

    @Test
    public void deleteDocId() {
        System.out.println(elasticSearchService.deleteDocId(POLICY, "__0V-QO89Eeu5bqoNtw7SfQ"));
    }
    
    @Autowired
    ElasticSearchService elasticSearchService;
    /**
     * Mybatis plus 流式读取数据同步到ES（ES批量保存操作）
     */
    public void dataSynchronizeEs() {
        List<EsPolicyPO> listEs = new ArrayList<>(500);
        policyInfoMapper.readStreamingData(resultContext -> {
            // 取数据库查询对象
            PolicyInfo policyInfo = resultContext.getResultObject();
            // 转换ES存储对象
            EsPolicyPO esPolicyPO = new EsPolicyPO();
            esPolicyPO.setId(policyInfo.getId());
            esPolicyPO.setTitle(policyInfo.getTitle());
            esPolicyPO.setIssueDate(policyInfo.getIssueDate());
            esPolicyPO.setSourceType(policyInfo.getSourceType());
            esPolicyPO.setFile(new EsPolicyPO.File(policyInfo.getFileName(), policyInfo.getFileExt()));
            listEs.add(esPolicyPO);
            if (listEs.size() == 500) {
                // ES批量保存文档
                elasticSearchService.batchInsertDoc("policy", listEs);
                listEs.clear();
            }
        });
        log.info("最后保存的集合数据大小位: {}", listEs.size());
        elasticSearchService.batchInsertDoc("policy", listEs);
		listEs.clear();
    }
}
```

#### 文档查询

