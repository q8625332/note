
**JAVA_OPTS**


| 字段                              | 描述                                                                                |
| ------------------------------- | --------------------------------------------------------------------------------- |
| -XX:+HeapDumpOnOutOfMemoryError | JVM 抛出 java.lang.OutOfMemoryError 时，通常只触发一次（OOM 后进程基本就挂了），堆内存用尽（已达到或超过 -Xmx4096m） |
| -XX:+HeapDumpBeforeFullGC       | 每次 Full GC 即将开始前，每次 Full GC 都会产生一个（非常频繁！），不一定跟内存使用量直接相关，而是 Full GC 触发时            |
| -XX:+HeapDumpAfterFullGC        | 每次 Full GC 结束后，每次 Full GC 都会产生一个（非常频繁！），同上，Full GC 触发时                            |
| -XX:HeapDumpPath=/efs/ljq/dump  | dump文件存储的地方                                                                       |
|                                 |                                                                                   |
