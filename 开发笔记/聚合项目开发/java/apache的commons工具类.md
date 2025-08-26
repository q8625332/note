## 计算耗时


```java
long startTime = System.currentTimeMillis();  
try {  
    Thread.sleep(2000L);  
} catch (InterruptedException e) {  
    throw new RuntimeException(e);  
}  
long endTime = System.currentTimeMillis();  
long consumeTime = endTime - startTime;  
String consumeText = DurationFormatUtils.formatDuration(consumeTime, "d'天' H'小时' m'分钟' s'秒'");  
System.out.println(consumeText);

// 结果：
0天 0小时 0分钟 2秒
```
