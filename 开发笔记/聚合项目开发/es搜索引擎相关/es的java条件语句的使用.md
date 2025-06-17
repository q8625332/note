
## SearchSourceBuilder

> SearchSourceBuilder搜索语句的Builder
> 用于构建查询的基本逻辑，比如查询数据的大小，排序等。

```java
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
```

> 将条件语句放入到查询中。


```java
// 条件语句在BoolQueryBuilder的方法里。可以参考BoolQueryBuilder的方法。
sourceBuilder.query(boolQueryBuilder);
```

**简单的例子**

```java
SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();  
BoolQueryBuilder boolQueryBuilder = QueryBuilders.boolQuery();  
  
// 添加accountId筛选条件  
boolQueryBuilder.must(QueryBuilders.termsQuery("userId.keyword", patternUpdateFlagDTO.getAccountIds()));  
  
// 添加updateTimestamp时间筛选条件  
boolQueryBuilder.must(QueryBuilders.rangeQuery("updated").gt(patternUpdateFlagDTO.getLastLooKFollowingTime()));  
  
sourceBuilder.query(boolQueryBuilder);  
// 只需要判断是否存在，因此size设置为1  
sourceBuilder.size(1);  
// 不需要返回实际数据  
sourceBuilder.fetchSource(false);  
// 找到一个匹配项后立即停止查询，模拟SQL EXISTS行为  
sourceBuilder.terminateAfter(1);  
  
try {  
    // 调用EsUtil中获取总命中数的方法  
    long count = EsUtil.countBySourceBuilder(PatternEsConstant.INDEX_ALIAS, sourceBuilder);  
    return count > 0;  
} catch (Exception e) {  
    LOGGER.error("hasFollowedAuthorsPublishedNewPatterns error", e);  
    return false;}

// esUtil
/**  
 * 根据查询构造器获取文档总数  
 * @param indexName           索引名字  
 * @param searchSourceBuilder 查询构造器  
 * @return 文档总数  
 */  
public static long countBySourceBuilder(String indexName, SearchSourceBuilder searchSourceBuilder) {  
    try {  
        RestHighLevelClient esClient = ESClient.getInstance();  
        if (esClient == null) {  
            return 0;  
        }  
        SearchRequest searchRequest = new SearchRequest(indexName);  
        // 只获取总数，不返回实际文档  
        searchSourceBuilder.size(0);  
        searchRequest.source(searchSourceBuilder);  
        SearchResponse searchResponse = esClient.search(searchRequest, RequestOptions.DEFAULT);  
        RestStatus status = searchResponse.status();  
        if (status.getStatus() == RestStatus.OK.getStatus()) {  
            SearchHits hits = searchResponse.getHits();  
            return hits.getTotalHits();  
        } else {  
            return 0;  
        }  
    } catch (Exception e) {  
        LOGGER.error("countBySourceBuilder error", e);  
        return 0;  
    }  
}
```

## BoolQueryBuilder

> BoolQueryBuilder条件语句的Builder
> 构建查询的条件，比如范围查询，in，=等。


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

### 常用方法：

- `.gte(value)`：大于等于（greater than or equal）
    
- `.gt(value)`：大于（greater than）
    
- `.lte(value)`：小于等于（less than or equal）
    
- `.lt(value)`：小于（less than）
    
- `.from(value)` / `.to(value)`：也可以用这两个方法设置范围（功能类似）

```java
boolQueryBuilder.must(QueryBuilders.rangeQuery("updateTimestamp").gt(patternUpdateFlagDTO.getLastLooKFollowingTime()));

// 时间范围查询
QueryBuilder query = QueryBuilders.rangeQuery("createTime")
    .from("2024-01-01T00:00:00")
    .to("2024-12-31T23:59:59");

```

> 聚合查询
> 比如 sum


```java
// 添加sum聚合  
sourceBuilder.aggregation(AggregationBuilders.sum("sum的别名").field("统计的字段"));
```


