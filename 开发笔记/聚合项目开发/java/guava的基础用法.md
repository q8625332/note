
## 限速器

```java
//每秒3次,超过返回false
private static final RateLimiter RATE_LIMITER = RateLimiter.create(3);
```

## Hashing
> ke'hu

```java
Hashing.murmur3_32().hashLong(circleId).asInt()
```
