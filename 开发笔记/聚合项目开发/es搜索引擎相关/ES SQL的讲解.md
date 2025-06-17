> 查询时的get方法
> get方式方式，pattern_store是index，类似sql中的table。
> 查询 _search

```es
GET /pattern_store/_search
```

**例子：**

| 字段   | 描述      |
| ---- | ------- |
| size | 查询的数量大小 |


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
