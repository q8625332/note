>`java.time.Duration` 是 Java 8 引入的 `java.time` 包中的一个类，用于表示时间量，以秒和纳秒为基本单位，通常用于表示两个时间点之间的时间间隔。它非常适合用于处理基于时间的持续时间（如秒数、分钟数、小时数等），但**不适用于日期** （如年、月、日），这些更适合用 `Period` 类处理。

## 🌟 一、`Duration` 的基本概念

`Duration` 表示一个时间间隔，而不是一个具体的时间点。它通常包含以下信息：

- 天数（days）
- 小时（hours）
- 分钟（minutes）
- 秒（seconds）
- 毫秒（milliseconds）
- 微秒（microseconds）

例如：

```java
Duration(seconds: 5) // 表示5秒的时间间隔
```
