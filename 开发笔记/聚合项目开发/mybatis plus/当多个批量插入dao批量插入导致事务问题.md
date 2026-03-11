
当我们有多个批量插入的dao，又在一个方法里有事务注解（@Transactional(rollbackFor = Exception.class)）。

当多个dao层数据批量插入的时候，触发了事务问题：


```
Caused by: org.springframework.dao.TransientDataAccessResourceException: Cannot change the ExecutorType when there is an existing transaction
```

解决办法是：

> 创建一个批量事务


```
@Autowired  
public RemotePointsBatchLocalService(SqlSessionFactory sqlSessionFactory) {  
    this.batchSqlSessionTemplate = new SqlSessionTemplate(sqlSessionFactory, ExecutorType.BATCH);  
}
```
然后将不同dao层的加入dao一个工厂里面：


```
UserPointDao batchUserPointDao = batchSqlSessionTemplate.getMapper(UserPointDao.class); 
 
UserPointHistoryDao batchPointHistoryDao = batchSqlSessionTemplate.getMapper(UserPointHistoryDao.class); 
 
PointsAdminRewardDetailDao batchRewardDetailDao = batchSqlSessionTemplate.getMapper(PointsAdminRewardDetailDao.class);
```

这样所有的批量处理都在同一个sql工厂里，就不会触发事务问题了。