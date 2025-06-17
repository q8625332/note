> SearchSourceBuilder搜索语句的Builder

```java
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
```

> BoolQueryBuilder条件语句的Builder


```java
BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();
```

>  **必须匹配（must）** 的查询条件，也就是逻辑上的 **AND** 条件


```java
boolQueryBuilder.must()
```

>