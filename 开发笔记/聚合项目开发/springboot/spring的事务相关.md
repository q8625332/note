
## 事务注解


```java
@Transactional(rollbackFor = Exception.class)
```

## 事务判断


```java
if (TransactionSynchronizationManager.isActualTransactionActive()) {  
    TransactionSynchronizationManager.registerSynchronization(new TransactionSynchronization() {  
        @Override  
        public void afterCommit() {  
            // 事务提交后，执行逻辑
        }  
    });  
} else {  
    // 事务不存在，执行逻辑
}
```
