
**创建文档**


```
PUT /forum_article_index
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 1,
    "analysis": {
      "normalizer": {
        "lowercase_normalizer": {
          "type": "custom",
          "char_filter": [],
          "filter": ["lowercase"]
        }
      }
    }
  },
  "properties": {
      "id": {
        "type": "keyword"
      },
      "created": {
        "type": "long"
      },
      "updated": {
        "type": "long"
      },
      "authorId": {
        "type": "keyword"
      },
      "title": {
        "type": "text", 
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 350
          }
        }
      },
      "content": {
        "type": "text" 
      },
      "viewCount": {
        "type": "integer"
      }
      "commentNum": {
        "type": "long"
      }
      "hotScore": {
        "type": "double"
      }
    }
}
```


| 字段                                       | 描述                                                           |
| ---------------------------------------- | ------------------------------------------------------------ |
| number_of_shards                         | 副本数量:一般以（节点数*1.5或3倍）来计算，比如有4个节点，分片数量一般是6个到12个，每个分片一般分配一个副本   |
| number_of_replicas                       | 副本                                                           |
| analysis.normalizer.lowercase_normalizer | 自定义 normalizer，对于type为keyword的字段，写入和查询时都会当做小写处理，从而实现忽略大小写的作用 |

**创建别名**


```
POST /_aliases
{
  "actions": [
    { "add": { "index": "forum_article_index", "alias": "forum_article" } }
  ]
}
```

**删除文档**


```
DELETE /forum_article_index
```
