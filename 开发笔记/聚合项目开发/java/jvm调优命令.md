
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

