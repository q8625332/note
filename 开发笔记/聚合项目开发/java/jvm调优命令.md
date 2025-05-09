
## 查看进程

```linux
ps -ef | grep java
```

## 查看java的内存

```linux
例子：
jstat -gccause <pid> <interval> <count>

jstat -gccause 147331 1000 100000
```

### 参数说明：

1. **`jstat`**  
    Java自带的监控工具，用于统计JVM性能数据。
    
2. **`-gccause`**  
    选项表示输出：
    
    - 垃圾回收（GC）的统计信息（与 `-gcutil` 相同）。
        
    - **最近一次GC的发生原因**（如 `Allocation Failure` 分配失败）。
        
    - **当前GC的触发原因**（如 `System.gc()` 或元空间不足等）。
        
3. **`147331`**  
    目标JVM进程的PID（进程ID），需替换为实际要监控的Java进程ID。
    
4. **`1000`**  
    采样间隔时间（毫秒），这里是每秒（1000ms）采集一次数据。
    
5. **`100000`**  
    总采样次数，这里会连续采集10万次（约100秒后结束，或手动终止）。


> 运行结果


![[Pasted image 20250509141013.png]]

