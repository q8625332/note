## 导包

```
<!--mybatis-plus start-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>3.1.0</version>
        </dependency>

        <!-- mybatis-plus自动模板引擎依赖 -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-generator</artifactId>
            <version>3.1.0</version>
        </dependency>

        <!-- mybatis-plus需要的模板引擎freemarker -->
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
        </dependency>
        <!--mybatis-plus end-->
```
## yml配置

```
mybatis-plus:
  configuration:
    #配置返回数据库(column下划线命名&&返回java实体是驼峰命名)，自动匹配无需as（没开启这个，SQL需要写as： select user_id as userId）
    map-underscore-to-camel-case: true
    cache-enabled: false
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl #打印sql语句,调试用
```
## bean 配置

```
	/**
     * 分页插件
     */
    @Bean
    public PaginationInterceptor paginationInterceptor() {
        return new PaginationInterceptor();
    }
	/**
     * 格式化打印 sql
     */
    @Bean
    public PerformanceInterceptor performanceInterceptor() {
        PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
        //格式化sql语句
        Properties properties = new Properties();
        properties.setProperty("format", "true");
        performanceInterceptor.setProperties(properties);
        return performanceInterceptor;
    }
```
## xml 编写大于等于，小于等于，出现报错的解决办法

> 由于在mybatis框架的xml中<= , >=解析会出现问题,编译报错,所以需要转译

**第一种写法：**
原符号 `< <= > >= & ’ "`
替换符号 `&lt; &lt;= &gt; &gt;= &amp; &apos; &quot;`

*详细表：*
|原符号| 替换符号 |
|:--:|:--:|
| < | `&lt;` |
| <= | `&lt;=` |
| >= | `&gt;=` |
| & | `&amp;` |
| ’ | `&apos;` |
| " | `&quot;` |

例如：sql如下：

```java
unix_timestamp(target.mc_end_date)&lt;= unix_timestamp(#{MonthEndTime})；unix_timestamp(target.mc_start_date) &gt;= unix_timestamp(#{MonthBeginTime})

```
**第二种写法：**
大于等于

```java
<![CDATA[ >= ]]>
```
小于等于

```java
<![CDATA[ <= ]]>
```
**例如：sql如下：**

```java
mc_end_date <![CDATA[ >= ]]> #{endTime} and  mc_start_date <![CDATA[ <= ]]> #{startTime}

```
## 更新时，实体传空，如何让字段不被过滤，而直接更新为空呢

> 可以使用以下注解，使其跳过空校验，让其传入什么参数就更新为什么参数

```java
@TableField(strategy = FieldStrategy.IGNORED)
private Long approvalTime;
```
