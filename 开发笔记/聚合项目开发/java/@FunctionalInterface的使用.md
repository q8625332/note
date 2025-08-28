> 比如我们在 `Supplier<?>`的抽象方法执行的时候，想要获取到方法前传入的参数时。
> 可以使用@FunctionalInterface来定义个**函数式接口**。

`@FunctionalInterface` 是 Java 8 引入的注解，用于标记一个接口为**函数式接口**。函数式接口是指**仅包含一个抽象方法**的接口，但可以包含多个默认方法或静态方法。这种接口的主要目的是支持 Lambda 表达式和方法引用。

## 接口定义解析

```java
@FunctionalInterface
public interface BatchConfigAwareOperation {
    int operate(BatchConfig config);
}
```

- `@FunctionalInterface`：注解表明这是一个函数式接口
- `BatchConfigAwareOperation`：接口名称
- `int operate(BatchConfig config)`：唯一的抽象方法，接受一个 `BatchConfig` 参数并返回 `int`

## 使用方法

### 1. 传统实现方式（匿名内部类）

```java
BatchConfigAwareOperation operation = new BatchConfigAwareOperation() {
    @Override
    public int operate(BatchConfig config) {
        // 实现具体的操作逻辑
        return config.getBatchSize() * 2;
    }
};
```

### 2. 使用 Lambda 表达式（推荐）

```java
// 简单的 Lambda 表达式
BatchConfigAwareOperation operation = (config) -> {
    return config.getBatchSize() * 2;
};

// 更简洁的写法（单行表达式）
BatchConfigAwareOperation operation = config -> config.getBatchSize() * 2;
```

### 3. 使用方法引用

如果已有方法符合接口签名：

```java
public class BatchProcessor {
    public static int calculateOperation(BatchConfig config) {
        return config.getBatchSize() * 2;
    }
    
    public int instanceOperation(BatchConfig config) {
        return config.getBatchSize() * 3;
    }
}

// 静态方法引用
BatchConfigAwareOperation operation = BatchProcessor::calculateOperation;

// 实例方法引用
BatchProcessor processor = new BatchProcessor();
BatchConfigAwareOperation operation = processor::instanceOperation;
```

## 实际应用场景

```java
public class BatchService {
    // 接受函数式接口作为参数
    public void processBatch(BatchConfig config, BatchConfigAwareOperation operation) {
        int result = operation.operate(config);
        System.out.println("操作结果: " + result);
        // 执行后续处理...
    }
    
    // 使用示例
    public void exampleUsage() {
        BatchConfig config = new BatchConfig(100); // 假设 BatchConfig 有构造函数
        
        // 传递不同的操作实现
        processBatch(config, cfg -> cfg.getBatchSize() * 2);
        processBatch(config, cfg -> cfg.getBatchSize() + 50);
        processBatch(config, cfg -> {
            // 复杂的操作逻辑
            int value = cfg.getBatchSize();
            if (value > 100) {
                return value / 2;
            } else {
                return value * 3;
            }
        });
    }
}
```

## 注意事项

1. **单抽象方法约束**：被 `@FunctionalInterface` 标记的接口只能有一个抽象方法
2. **默认方法**：可以包含多个默认方法而不违反函数式接口的定义
3. **静态方法**：可以包含任意数量的静态方法
4. **Object 类的方法**：从 Object 类继承的方法不计入抽象方法数量

## 设计意图

这种设计模式常用于：
- 策略模式实现
- 回调机制
- 简化代码结构
- 提高代码的可复用性和灵活性

通过使用函数式接口，你可以将行为（操作逻辑）作为参数传递，使代码更加模块化和可定制。

## 实战方法

> 参谋考另外一篇文章

[[批量处理工具]]