> 查询时的get方法
> get方式方式，pattern_store是index，类似sql中的table。
> 查询 _search

```es
GET /pattern_store/_search
```

**例子：**

| 字段              | 描述                                                                                  |
| --------------- | ----------------------------------------------------------------------------------- |
| size            | 查询的数量大小                                                                             |
| terminate_after | 查询到一条就停止                                                                            |
| query           | 查询条件的构造体                                                                            |
| bool            | 条件语句                                                                                |
| must            | 精准匹配                                                                                |
| terms           | 多个数据匹配，类似 in                                                                        |
| *.keyword       | accountId.keyword 这个写法，代表全量匹配，就是字段需要全部命中，为什么要这么写呢？是因为es里面的字段是有分词器的，如果要全量匹配的话，需要指定好。 |
| range           | 范围查询，如数字的范围查询，时间的范围查询。                                                              |
| from            | 时间查询的开始                                                                             |
| to              | 时间查询的结束                                                                             |
| gt              | 大于                                                                                  |
| gte             | 大于等于                                                                                |
| lt              | 小于                                                                                  |
| lte             | 小于等于                                                                                |


```es
{
  "size": 0,
  "terminate_after": 1,
  "query": {
    "bool": {
      "must": [
        {
          "terms": {
            "accountId.keyword": [
              "11-f8a8b78824a94ca7b5fe8e7f2e605441"
            ]
          }
        },
        {
          "range": {
            "updateTimestamp": {
              "from": 0,
              "to": null,
              "include_lower": false,
              "include_upper": true
            }
          }
        }
      ],
      "adjust_pure_negative": true
    }
  }
}
```
