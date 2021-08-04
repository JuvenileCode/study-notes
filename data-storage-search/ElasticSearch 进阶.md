## ElasticSearch 进阶

[参考](https://zq99299.github.io/note-book/elasticsearch-senior/)

### 结构化搜索





### 聚合分析

#### 核心概念

**bucket（桶）：**表示一个数据分组，类似 数据库 中的 group操作

**metric：**metric 就是对没一个 bucket 执行的某种聚合分析的操作，比如说求平均值、求最大值、求最小值

- count：bucket terms，自动就会有一个 doc_count，就相当于是 count
- avg：avg aggs，求平均值
- max：求一个 bucket 内，指定 field 值最大的那个数据
- min：求一个 bucket 内，指定 field 值最小的那个数据
- sum：求一个 bucket 内，指定 field 值的总和



#### 聚合算法

**易并行算法 max**

有些聚合分析的算法，是很容易就可以并行的，比如说 max，流程如下：

- 假设有 3 和 shard 查找最大值
- 每个 shard 查找并返回一个最大值
- 在聚合节点上会拿到 3 个值，然后在这三个值中排序出最大的一个即可

有些聚合分析的算法是不好并行的，比如说 `count(distinct)`（去重后统计剩余数量，也就是唯一值个数）， 并不是说在每个 node 上直接就出一些 distinct value 就可以的，因为每个 shard 之间可能还有重复的数据，所以就不准的， 这个时候就需要从每个 shard 上把所有相关数据都获取过来。

不易并行算法进行聚合的时候，由于数据量可能太大会导致内存占用过多，性能严重影响，甚至内存溢出， 这个时候 es 会采取近似聚合的方式，就是采用在每个 node 上进行近估计的方式， 得到最终的结论，如：`cuont(distcint)` 本来是 100 万，可能会返回 105 万， 近似估计后的结果，不完全准确，但是速度会很快，一般会达到完全精准的算法的性能的数十倍。

这就会出现一个概念，三角选择原则

**三角选择原则**

精准 + 实时 + 大数据只能选择 2 个

- 精准+实时: 没有大数据，数据量很小，那么一般就是单机跑，随便你则么玩儿就可以
- 精准+大数据：hadoop 批处理，非实时，可以处理海量数据，保证精准，可能会跑几个小时
- 大数据+实时：es，不精准，近似估计，可能会有百分之几的错误率

**进似聚合算法**

- 如果采取近似估计的算法：延时在 100ms 左右，0.5% 错误
- 如果采取 100% 精准的算法：延时一般在 ns~几十s，甚至几十分钟，几小时， 0% 错误



#### 统计、评价、多次下钻

模拟数据

```json
/* mappings 定义 */
{
	"mappings": {
		"sales": {
			"properties": {
				"price": {
					"type": "long"
				},
				"color": {
					"type": "keyword"
				},
				"brand": {
					"type": "keyword"
				},
				"sold_date": {
					"type": "date"
				}
			}
		}
	}
}
/* 插入部分数据 */
{ "index": {}}
{ "price" : 1000, "color" : "红色", "brand" : "长虹", "sold_date" : "2016-10-28" }
{ "index": {}}
{ "price" : 2000, "color" : "红色", "brand" : "长虹", "sold_date" : "2016-11-05" }
{ "index": {}}
{ "price" : 3000, "color" : "绿色", "brand" : "小米", "sold_date" : "2016-05-18" }
{ "index": {}}
{ "price" : 1500, "color" : "蓝色", "brand" : "TCL", "sold_date" : "2016-07-02" }
{ "index": {}}
{ "price" : 1200, "color" : "绿色", "brand" : "TCL", "sold_date" : "2016-08-19" }
{ "index": {}}
{ "price" : 2000, "color" : "红色", "brand" : "长虹", "sold_date" : "2016-11-05" }
{ "index": {}}
{ "price" : 8000, "color" : "红色", "brand" : "三星", "sold_date" : "2017-01-01" }
{ "index": {}}
{ "price" : 2500, "color" : "蓝色", "brand" : "小米", "sold_date" : "2017-02-12" }
```

##### 按颜色统计销量

```json
{
  "size": 0,
  "aggs": {
    "popular_colors": {
      "terms": {
        "field": "color"
      }
    }
  }
}
```

- size：只获取聚合结果，而不要返回参与聚合的原始数据
- aggs：固定语法，要对一份数据执行分组聚合操作
- popular_colors：就是对每个 aggs 都要起一个名字，这个名字是自定义的
- terms：根据字段的值进行分组
- field：根据指定的字段的值进行分组

```json
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 8,
    "max_score": 0,
    "hits": []
  },
  "aggregations": {
    "popular_colors": {
      "doc_count_error_upper_bound": 0,
      "sum_other_doc_count": 0,
      "buckets": [
        {
          "key": "红色",
          "doc_count": 4
        },
        {
          "key": "绿色",
          "doc_count": 2
        },
        {
          "key": "蓝色",
          "doc_count": 2
        }
      ]
    }
  }
}
```

- hits.hits：我们指定了size=0，所以 hits.hits 就是空的，否则会把执行聚合的那些原始数据给你返回回来
- aggregations：聚合结果
- popular_color：我们指定的某个聚合的名称
- buckets：根据我们指定的 field 划分出的 buckets
- key：每个 bucket 对应的那个值
- doc_count：这个 bucket 分组内，有多少个数据
- 数量，其实就是这种颜色的销量
- 默认的排序规则：按照 doc_count 降序排序

##### 按颜色统计再算评价价格

```json
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
            "avg_price": {
               "avg": {
                  "field": "price"
               }
            }
         }
      }
   }
}
```

##### 嵌套+多层次下钻

下钻的意思是，已经分了一个组了，比如说颜色的分组，然后还要继续对这个分组内的数据再分组， 比如一个颜色内，还可以分成多个不同的品牌的组，最后对每个最小粒度的分组执行聚合分析操作， 这就叫做下钻分析

```json
/* 按照多个维度（颜色+品牌）多层下钻分析，而且学会了每个下钻维度（颜色，颜色+品牌） */
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
            "color_avg_price": {
               "avg": {
                  "field": "price"
               }
            },
            "group_by_brand":{
              "terms": {
                "field": "brand"
              },
              "aggs": {
                "brand_avg_price": {
                  "avg": {
                    "field": "price"
                  }
                }
              }
            }
         }
      }
   }
}
```



#### histogram 区间分组

什么是 histogram ？：类似于 terms，也是进行 bucket 分组操作，接收一个 field， 按照这个 field 的值的各个范围区间，进行 bucket 分组操作

##### 统计每隔 2000 价格区间内的电视数量和总价

```json
/* interval：2000，根据字段 price 的值划分范围，0~2000，2000~4000 ... */
{
   "size" : 0,
   "aggs":{
      "price":{
         "histogram":{
            "field": "price",
            "interval": 2000
         },
         "aggs":{
            "revenue": {
               "sum": {
                 "field" : "price"
               }
             }
         }
      }
   }
}
```

##### 时间范围需求：2016-01-01 ~ 2017-12-31 之间每个月的电视销量

```json
{
   "size" : 0,
   "aggs": {
      "sales": {
         "date_histogram": {
            "field": "sold_date",
            "interval": "month",
            "format": "yyyy-MM-dd",
            "min_doc_count" : 0,
            "extended_bounds" : {
                "min" : "2016-01-01",
                "max" : "2017-12-31"
            }
         }
      }
   }
}
```

这里就用到了 `date_histogram` 日期区间分组语法

- min_doc_count：

  即使某个日期 interval，2017-01-01~2017-01-31 中，一条数据都没有，那么这个区间也是要返回的，不然默认是会过滤掉这个区间的

- extended_bounds

  min，max：划分 bucket 的时候，会限定在这个起始日期，和截止日期内

##### 下钻分析之统计每季度每个品牌的销售额和季度总销售额

```json
{
   "size": 0,
   "aggs": {
     "dales": {
       "date_histogram": {
         "field": "sold_date",
         "interval": "quarter",
         "format": "yyyy-MM-dd",
         "min_doc_count": 0,
         "extended_bounds":{
          "min" : "2016-01-01",
          "max" : "2017-12-31"
         }
       },
       "aggs": {
         "group_brand": {
           "terms": {
             "field": "brand"
           },
           "aggs": {
              "brand_sales": {
               "sum": {
                 "field": "price"
               }
             }
           }
         },
         "total_sales":{
           "sum": {
             "field": "price"
           }
         }
       }
     }
   }
}
```



#### 搜索、聚合排序

在 es 中来说，aggregation 聚合操作必须在一个 scope 中执行，搜索出来的结果就是聚合分析操作的 scope

##### 统计某品牌颜色分类数量

```json
{
   "size": 0,
   "query": {
     "term": {
       "brand": {
         "value": "小米"
       }
     }
   },
   "aggs": {
     "group_by_color": {
       "terms": {
         "field": "color"
       }
     }
   }
}
```

#####  global bucket 语法

考虑一个场景出来两个结果：一个结果是基于 query 搜索结果来聚合的， 一个结果是对所有数据执行聚合的。 可以理解为在一个搜索中针对不同的 scope 进行聚合操作。

计算长虹的平均价格和所有品牌的平均价格，形成一个对比。

```json
/* global：就是 global bucket，将所有数据纳入聚合的 scope，而不管之前的 query */
{
  "size": 0,
  "query": {
    "term": {
      "brand": {
        "value": "长虹"
      }
    }
  },
  "aggs": {
    "single_brand_avg_price": {
      "avg": {
        "field": "price"
      }
    },
    "all": {
      "global": {},
      "aggs": {
        "all_brand_avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

##### filter+aggs：统计价格大于 1200 的电视平均价格

```json
{
  "size": 0,
  "query": {
    "constant_score": {
      "filter": {
        "range": {
          "price": {
            "gte": 1200
          }
        }
      }
    }
  },
  "aggs": {
    "avg_price": {
      "avg": {
        "field": "price"
      }
    }
  }
}
```

##### bucket filter 与 query filter 

需求：统计长虹电视过去 130 天、140 天、150 天的平均销售价格

>bucket filter 与 query filter 区别，bucket.filter 针对的是聚合去做的，如果放 query 里面的 filter，是全局的，会对所有的数据都有影响

```json
{
  "size": 0,
  "query": {
    "term": {
      "brand": {
        "value": "长虹"
      }
    }
  },
  "aggs": {
    "recent_150d": {
      "filter": {
        "range": {
          "sold_date": {
            "gte": "now-150d"
          }
        }
      },
      "aggs": {
        "recent_150d_avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    },
    "recent_140d": {
      "filter": {
        "range": {
          "sold_date": {
            "gte": "now-140d"
          }
        }
      },
      "aggs": {
        "recent_140d_avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    },
    "recent_130d": {
      "filter": {
        "range": {
          "sold_date": {
            "gte": "now-130d"
          }
        }
      },
      "aggs": {
        "recent_130d_avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

##### order：按每种颜色的平均销售额升序排序

```json
{
  "size": 0,
  "aggs": {
    "group_by_color": {
      "terms": {
        "field": "color",
        "order": {
          "avg_price": "asc"
        }
      },
      "aggs": {
        "avg_price": {
          "avg": {
            "field": "price"
          }
        }
      }
    }
  }
}
```

排序，在下钻的聚合操作上排序

##### 按颜色分组，并统计每个品牌的平均价格，且按平均价格排序

```json
{
  "size": 0,
  "aggs": {
    "group_by_color": {
      "terms": {
        "field": "color"
      },
      "aggs": {
        "group_by_brand": {
          "terms": {
            "field": "brand",
            "order": {
              "avg_price": "desc"
            }
          },
          "aggs": {
            "avg_price": {
              "avg": {
                "field": "price"
              }
            }
          }
        }
      }
    }
  }
}
```

#### cardinality 去重算法