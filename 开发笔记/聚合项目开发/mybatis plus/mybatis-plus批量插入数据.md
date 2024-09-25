## saveBatch

> 将传入的实体List分为1000个一批，每个调用sqlSession.insert(sqlStatement, entity)，insert完一批做一次
> 特点：分批插入，batchsize分为一次sql提交  
如果有10000条数据，batchSize为1000，则分为10次事务提交，每个事务提交1000条insert语句