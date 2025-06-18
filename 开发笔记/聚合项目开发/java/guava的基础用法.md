
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
