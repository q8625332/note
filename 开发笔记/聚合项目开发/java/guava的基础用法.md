
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
