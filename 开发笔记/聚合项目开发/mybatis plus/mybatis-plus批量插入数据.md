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

> sqlSession.flushStatements()，看起来是没有问题，但是就是速度非常慢。查阅相关资料发现，要批量执行的话，JDBC连接URL字符串中需要新增一个参数：`rewriteBatchedStatements=true`
> 
> MySQL的JDBC连接的url中要加rewriteBatchedStatements参数，并保证5.1.13以上版本的驱动，才能实现高性能的批量插入。
> 
> MySQL JDBC驱动在默认情况下会无视executeBatch()语句，把我们期望批量执行的一组sql语句拆散，一条一条地发给MySQL数据库，批量插入实际上是单条插入，直接造成较低的性能。 
> 
> 只有把rewriteBatchedStatements参数置为true, 驱动才会帮你批量执行SQL  另外这个选项对INSERT/UPDATE/DELETE都有效

**解决：**

>在jdbc连接url最后加上rewriteBatchedStatements=true，测试发现速度果然提升

```
jdbc:mysql://172.29.1.100:5000/bd_cloud_vehicle_dev?rewriteBatchedStatements=true
```


####  然而可以重写 批量插入的方法

#### InsertBatchSomeColumn（批量插入 仅适用于mysql，一次插入多条数据）

> 最后会拼接为如下sql： insert into table values (x1, y1, z1), (x2, y2, z2), (x…, y…, z…);

> 特点：拼接成一个sql提交

开启方式：需要我们配合SQL注入器来开启，分为三个步骤：

1、自定义SQL注入器

新建一个名为InsertBatchSqlInjector 的类，继承DefaultSqlInjector，，添加InsertBatchSomeColumn方法（当然，类名可以根据自己的喜好来）

```java
public class MySqlInjector extends DefaultSqlInjector {

    @Override
    public List<AbstractMethod> getMethodList(Class<?> mapperClass, TableInfo tableInfo) {
        List<AbstractMethod> methodList = super.getMethodList(mapperClass, tableInfo);
        methodList.add(new InsertBatchSomeColumn(i -> i.getFieldFill() != FieldFill.UPDATE));
        return methodList;
    }
}
```

2、将SQL注入器交给Spring容器

在MybatisPlusConfig类中，将刚才创建的SQL注入器InsertBatchSqlInjector 注册为一个bean。

```java
@Configuration
public class MybatisPlusConfig {

    @Bean
    public MySqlInjector sqlInjector() {
        return new MySqlInjector();
    }
}
```

3、编写自定义MyBaseMapper继承BaseMapper，并编写insertBatchSomeColumn()方法

新建MyBaseMapper类，继承BaseMapper，并在此类中配置insertBatchSomeColumn()方法。

```java
public interface MyBaseMapper<T> extends BaseMapper<T> {
    /**
     * 以下定义的 4个 method 其中 3 个是内置的选装件
     */
    int insertBatchSomeColumn(List<T> entityList);
}
```

4、需要批量插入的Mapper继承自定义MyBaseMapper

```java
@Mapper
public interface StudentMapper extends MyBaseMapper<Student> {

    @SelectProvider(value = MySelectProvider.class, method = "getSql")
    Student select(String sql);
}
```