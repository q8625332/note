以下是针对 Java 环境的几种实现方案，处理 UUID 用户 ID 的超大规模在线状态存储。

## 方案1：分片 Bitmap 实现（推荐）

```java
import redis.clients.jedis.Jedis;
import java.nio.charset.StandardCharsets;
import java.util.UUID;

public class BitmapOnlineStatus {
    private final Jedis jedis;
    private static final int SHARD_SIZE = 1_000_000; // 每个分片100万位
    
    public BitmapOnlineStatus(Jedis jedis) {
        this.jedis = jedis;
    }
    
    public void setOnline(UUID userId) {
        long position = hashUserId(userId);
        int shard = (int) (position / SHARD_SIZE);
        long offset = position % SHARD_SIZE;
        
        jedis.setbit("online_shard:" + shard, offset, true);
        jedis.expire("online_shard:" + shard, 60); // 60秒过期
    }
    
    public boolean isOnline(UUID userId) {
        long position = hashUserId(userId);
        int shard = (int) (position / SHARD_SIZE);
        long offset = position % SHARD_SIZE;
        
        return jedis.getbit("online_shard:" + shard, offset);
    }
    
    private long hashUserId(UUID userId) {
        // 使用MD5部分字节转换为long
        byte[] md5 = DigestUtils.md5(userId.toString());
        return ((md5[0] & 0xFFL) << 56) | 
               ((md5[1] & 0xFFL) << 48) |
               ((md5[2] & 0xFFL) << 40) |
               ((md5[3] & 0xFFL) << 32) |
               ((md5[4] & 0xFFL) << 24) |
               ((md5[5] & 0xFFL) << 16) |
               ((md5[6] & 0xFFL) << 8)  |
               (md5[7] & 0xFFL);
    }
}
```


**优点**：内存效率极高，100万用户只需约128KB内存  
**缺点**：有极低概率的哈希冲突

## 方案2：RedisBloom 布隆过滤器（需要Redis模块）


```java
import redis.clients.jedis.Jedis;
import java.util.UUID;

public class BloomFilterOnlineStatus {
    private final Jedis jedis;
    
    public BloomFilterOnlineStatus(Jedis jedis) {
        this.jedis = jedis;
    }
    
    public void setOnline(UUID userId) {
        // 需要RedisBloom模块支持
        jedis.sendCommand(
            "BF.ADD", "online_users", userId.toString()
        );
    }
    
    public boolean isOnline(UUID userId) {
        // 返回1表示可能存在，0表示肯定不存在
        return jedis.sendCommand(
            "BF.EXISTS", "online_users", userId.toString()
        ).equals(1L);
    }
}
```



**依赖**：


```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>4.3.1</version>
</dependency>
```


## 方案3：Redisson 分布式布隆过滤器


```java
import org.redisson.Redisson;
import org.redisson.api.RBloomFilter;
import org.redisson.api.RedissonClient;
import java.util.UUID;

public class RedissonBloomFilter {
    private final RBloomFilter<String> bloomFilter;
    
    public RedissonBloomFilter() {
        RedissonClient redisson = Redisson.create();
        bloomFilter = redisson.getBloomFilter("online_users");
        bloomFilter.tryInit(100_000_000, 0.01); // 1亿容量，1%误判率
    }
    
    public void setOnline(UUID userId) {
        bloomFilter.add(userId.toString());
    }
    
    public boolean isOnline(UUID userId) {
        return bloomFilter.contains(userId.toString());
    }
}
```

**依赖**：


```xml
<dependency>
    <groupId>org.redisson</groupId>
    <artifactId>redisson</artifactId>
    <version>3.17.7</version>
</dependency>
```

## 方案4：多级缓存策略


```java
import redis.clients.jedis.Jedis;
import java.util.UUID;
import java.util.concurrent.TimeUnit;

public class MultiLevelOnlineStatus {
    private final Jedis jedis;
    private final Cache<UUID, Boolean> localCache;
    
    public MultiLevelOnlineStatus(Jedis jedis) {
        this.jedis = jedis;
        this.localCache = Caffeine.newBuilder()
            .expireAfterWrite(10, TimeUnit.SECONDS)
            .maximumSize(100_000)
            .build();
    }
    
    public void setOnline(UUID userId) {
        // 1. 本地缓存
        localCache.put(userId, true);
        
        // 2. Redis短期精确存储
        jedis.setex("online_exact:" + userId, 30, "1");
        
        // 3. 布隆过滤器
        jedis.bfAdd("online_bloom", userId.toString());
    }
    
    public boolean isOnline(UUID userId) {
        // 1. 检查本地缓存
        Boolean cached = localCache.getIfPresent(userId);
        if (cached != null) return cached;
        
        // 2. 检查Redis精确存储
        if (jedis.exists("online_exact:" + userId)) {
            return true;
        }
        
        // 3. 检查布隆过滤器
        return jedis.bfExists("online_bloom", userId.toString());
    }
}
```

## 性能对比

|方案|内存占用|精确性|适合规模|实现复杂度|
|---|---|---|---|---|
|分片Bitmap|极低|可能有冲突|超大规模|中|
|RedisBloom|低|有误判|大规模|低|
|Redisson布隆|低|有误判|大规模|低|
|多级缓存|中|精确|各种规模|高|
