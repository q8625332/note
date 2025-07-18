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
Duration duration = Duration.ofSeconds(125);

System.out.println(duration.getSeconds()); // 125
System.out.println(duration.toMinutes());  // 2
System.out.println(duration.toHours());    // 0
System.out.println(duration.toDays());     // 0
```

在 Java 的 `Duration` 中，你可能会看到类似 `PT1H30M45.123S` 这样的字符串表示形式，这就是 **ISO 8601 标准中用于表示持续时间（Duration）的格式** ，其中的 `PT` 是这个格式中的一个关键部分。

## 🔍 一、什么是 PT？

在 `Duration` 的字符串表示中：


```java
PT1H30M45S
```


- `PT` 是 **"Period Time"** 的缩写。
- 它表示这个字符串描述的是一个 **时间持续长度（duration）** ，而不是一个具体的日期或时间点。
- `PT` 后面的部分表示具体的持续时间，由以下单位组成：
    - `H`：小时（Hour）
    - `M`：分钟（Minute）
    - `S`：秒（Second）
    - `MS` 或 `.nnnnnnnnn`：毫秒或纳秒（在字符串中以小数点表示）

## 📌 二、格式说明

完整的格式如下：

```java
PnYnMnDTnHnMn.nS
```

|   |   |   |
|---|---|---|
|P|表示 Period 的开始（Duration 也用）|P1D|
|Y|年（Year）|P2Y → 2年|
|M|月（Month）|P3M → 3个月|
|D|天（Day）|P10D → 10天|
|T|表示“时间”部分开始（Time）|PT1H → 1小时|
|H|小时（Hour）|PT2H → 2小时|
|M|分钟（Minute）|PT30M → 30分钟|
|S|秒（Second）|PT45S → 45秒|