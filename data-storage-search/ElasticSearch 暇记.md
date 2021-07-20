## ElasticSearch 暇记

### Http Api 

```bash
# ES设置登录账户和密码需要在heard里设置 Authorization

# 查询ES存在的所有索引
###
GET http://192.168.1.50:9200/_cat/indices?v
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=

###

# 删除某个索引
###
DELETE http://192.168.1.50:9200/kibana_sample_data_ecommerce
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=

###

# 查询索引下的数据
###
GET http://101.132.17.49:9200/policy_store_policy_file_1626753758853/_doc/_search?pretty=true
Accept: */*
Cache-Control: no-cache
Authorization: Basic ZWxhc3RpYzoxMjM0NTY=
###
```



### Es Mapping



### JAVA Api

