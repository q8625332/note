> SearchSourceBuilder搜索语句的Builder

```java
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
```

> BoolQueryBuilder条件语句的Builder


```java
BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
```

> 查询相当于 = 


```java
boolQueryBuilder.must（
```
