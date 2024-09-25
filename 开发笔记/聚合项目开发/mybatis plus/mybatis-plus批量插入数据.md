#### saveBatch

> 将传入的实体List分为1000个一批，每个调用sqlSession.insert(sqlStatement, entity)，insert完一批做一次
> 特点：分批插入，batchsize分为一次sql提交 ,如果有10000条数据，batchSize为1000，则分为10次事务提交，每个事务提交1000条insert语句

1. 继承ServiceImpl

```java
public class AppAttentionServiceImpl extends ServiceImpl<AttentionMapper,AttentionDO>  implements AppAttentionService  {}
```

2. 使用saveBatch

```java
this.saveBatch(infoList);
this.saveBatch(infoList,1000);//每1000个拼接成一个sql进行提交
```

##### 这样会非常慢？

