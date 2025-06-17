
## SearchSourceBuilder

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

>  termsQuery
>  Elasticsearch Java 客户端中用于构建 **精确匹配多个值** 的查询条件。它的作用是：查询某个字段的值是否在你提供的一组值中，类似于 SQL 中的 `IN` 语句。


```java
boolQueryBuilder.must(QueryBuilders.termsQuery("userId.keyword", patternUpdateFlagDTO.getAccountIds()));
```

> rangeQuery
> Elasticsearch Java 客户端中用来构建 **范围查询** 的方法，适用于查找某个字段的值在一定区间内的文档。它常用于时间、数字、价格等连续值的筛选。


```java
boolQueryBuilder.must(QueryBuilders.rangeQuery("updateTimestamp").gt(patternUpdateFlagDTO.getLastLooKFollowingTime()));
```
