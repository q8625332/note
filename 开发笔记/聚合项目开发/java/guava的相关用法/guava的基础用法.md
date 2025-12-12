
## 限速器

```java
//每秒3次,超过返回false
private static final RateLimiter RATE_LIMITER = RateLimiter.create(3);
```

## Hashing

> 可以使用这个方法，取hash值

```java
Hashing.murmur3_32().hashLong(circleId).asInt()
```

## difference

> 返回交集的数据

```java
Sets.difference(Sets.newHashSet(fromIds), Sets.newHashSet(userIds));
```


## symmetricDifference

> 返回没有交集的数据

```java
Sets.symmetricDifference(Sets.newHashSet(fromIds), Sets.newHashSet(userIds));
```


## 批量拆解器

> 分批使用


```
List<List<Long>> partition = Lists.partition(articleIds, 500);  
Iterator<List<Long>> iterator = partition.iterator();

// 判断是否还有下一页
if (iterator.hasNext()) {}

// 获取当前这一批
List<Long> batchIds = iterator.next();

```

