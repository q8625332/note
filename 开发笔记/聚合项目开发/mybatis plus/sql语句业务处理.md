## sql -业务解决笔记
**模糊搜索更具搜索的关键字越精准排序**

```sql
select *
from ccb_user.ccb_user
where nick_name like concat('%',#{keyword},'%')
order by locate(#{keyword}, nick_name), char_length(nick_name);
```

## mysql 根据传入的id顺序进行排序

```java
SELECT * FROM table ORDER BY FIELD(id, 1, 2, 3, 4);
```
## sql先排序后分组的例子

> 再业务中我们会遇到需要排序后分组的例子，但是sql的分组默认是那最小id的那一条数据，不符合我们的业务。所以参考以下例子：


> 例子：
> 一个表有四个字段id，out_trade_no，fees_pay_id，created。其中id和out_trade_no两个字段是唯一的，根据传入的一批fees_pay_id集合，先根据created倒序排，后根据fees_pay_id分组，获取fees_pay_id的每一个out_trade_no的是created倒序的最新一条数据。sql怎么写
> 以下是可以用于实现所需功能的 SQL 查询语句：

```sql
SELECT t1.id, t1.out_trade_no, t1.fees_pay_id, t1.created
FROM your_table_name t1
INNER JOIN (
  SELECT fees_pay_id, MAX(created) AS max_created
  FROM your_table_name
  WHERE fees_pay_id IN (your_list_of_fees_pay_ids)
  GROUP BY fees_pay_id
) t2 ON t1.fees_pay_id = t2.fees_pay_id AND t1.created = t2.max_created
GROUP BY t1.fees_pay_id, t1.out_trade_no, t1.id
ORDER BY t1.created DESC, t1.fees_pay_id
```
**解释如下：**

> 注意，这个查询语句假设 id 和 out_trade_no 字段都是唯一的。如果 out_trade_no 不唯一，需要对查询语句进行修改，例如使用子查询或窗口函数等方式来选取每个 fees_pay_id 中最新的 out_trade_no。

 1. your_table_name 是存储数据的表名。
 2. your_list_of_fees_pay_ids 是需要查询的 fees_pay_id 的集合，可以替换为具体的值或变量。
 3. 首先使用子查询 t2 来查找每个 fees_pay_id 中创建时间最晚的记录，并以 fees_pay_id 为分组字段。
 4. 然后将 t1 表与 t2 表进行内连接，以 fees_pay_id 和 created 字段匹配，从而得到 fees_pay_id 中创建时间最晚的一条记录，并选出对应的 id、out_trade_no 和 created 字段。
 5. 使用 GROUP BY 子句将结果按 fees_pay_id、out_trade_no 和 id 进行分组，以便保证每个 fees_pay_id 中的 out_trade_no 只选取一次，并且在 MySQL 5.7 中，对于非聚合列（即 id 和 out_trade_no），必须将其列入分组依据，否则查询会失败。
 6. 最后按照 created 字段的倒序和 fees_pay_id 字段进行排序，得到最终结果。

> 例子2：可以通过窗口函数去实现排序
> `row_number() over (PARTITION BY dysds.warehouseId order by id desc) as rowNum`
> chuang

```sql
select dysds.*  
from (select dysds.warehouseId as warehouseId,  
dysds.quantity as yesterdayQuantity,  
row_number() over (PARTITION BY dysds.warehouseId order by id desc) as rowNum  
from dye_yarn_stock_daily_stat dysds  
where dysds.tenantId = #{req.tenantId}  
and dysds.createdTime &lt; #{req.startTime}) as dysds  
where dysds.rowNum = 1  
group by dysds.warehouseId
```

## 获取两级树名称
**表**

```java
CREATE TABLE `p_product_category` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
  `merchant_id` bigint NOT NULL COMMENT '商家id',
  `name` varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '名称',
  `description` varchar(500) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '描述',
  `image` varchar(500) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '分类图片',
  `sort` int NOT NULL DEFAULT '0' COMMENT '排序序号',
  `parent_id` bigint NOT NULL DEFAULT '0' COMMENT '父类目id，根级节点默认为0',
  `leaf` tinyint(1) NOT NULL DEFAULT '1' COMMENT '是否叶子节点',
  `hierarchy` int DEFAULT '0' COMMENT '层级',
  `created` bigint NOT NULL COMMENT '创建时间',
  `updated` bigint DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=314 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci COMMENT='商品类目表';
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/ee8fd0efbf7840d099957e03e7013893.png)
**sql**

```java
SELECT
	d.id,
	group_concat( c.NAME SEPARATOR ' > ' ) AS NAME
FROM
	p_product_category c
	INNER JOIN (
	SELECT
		a.id,
		substring_index( substring_index( a.path, ',', b.help_topic_id + 1 ), ',', - 1 ) AS pid
	FROM
		(
		SELECT
			cat.id,
			concat( cat.parent_id, ',', cat.id ) path 
		FROM
			p_product_category cat 
		WHERE
		cat.id IN ( 65 )) a
		INNER JOIN mysql.help_topic b ON b.help_topic_id < ( length( a.path ) - length( REPLACE ( a.path, ',', '' )) + 1 ) 
	ORDER BY
		pid 
	) d ON d.pid = c.id 
GROUP BY
	d.id
```
**实现效果**
![在这里插入图片描述](https://img-blog.csdnimg.cn/9b1496ecd372480dbb3b8ce3d9539281.png)
## 获取树名字（无限级别的）
**表**

```java
CREATE TABLE `category` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
  `name` varchar(100) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '类目名称',
  `path` varchar(500) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NOT NULL COMMENT '类目路径',
  `parent_id` bigint DEFAULT NULL COMMENT '父类目id',
  `created` bigint NOT NULL COMMENT '创建时间',
  `filter` tinyint(1) NOT NULL COMMENT '是否筛选项',
  `sort` int NOT NULL COMMENT '排序号',
  `type` int DEFAULT NULL COMMENT '分类类型 1 = 资讯 2 = 活动',
  `editor_id` bigint DEFAULT NULL COMMENT '编辑人用户id',
  `editor_name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci DEFAULT NULL COMMENT '编辑人用户名',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=3321 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci ROW_FORMAT=DYNAMIC;
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/579b121ca9f0419caab173bb50290d40.png)
**sql**

```java
SELECT
	e.id,
	group_concat( d.NAME ) AS category_name 
FROM
	cca_basic.category d
	INNER JOIN (
	SELECT
		a.id,
		substring_index( substring_index( a.path, '/', b.help_topic_id + 1 ), '/', - 1 ) AS npath 
	FROM
		(
		SELECT
			id,
			concat( substr( c.path, 5 ), id ) AS path 
		FROM
			cca_basic.category c 
		WHERE
		id IN (3288)) a
	INNER JOIN mysql.help_topic b ON b.help_topic_id < ( length( a.path ) - length( REPLACE ( a.path, '/', '' )) + 1 )) e ON e.npath = d.id 
GROUP BY
	e.id;
```
**效果**
![在这里插入图片描述](https://img-blog.csdnimg.cn/760212f091bb493091ddc1aa13f570fc.png)
## 查询json字段的演示
**例子**

> 字段名是`transportUser`
> mysql 的json字段模糊查询[{"transportUser": 474, "transportUserName": "天乡"},{"transportUser": 473, "transportUserName": "天乡"},{"transportUser": 475, "transportUserName": "天乡"}]里的transportUser为474或者475的数据

```java
[{"transportUser": 474, "transportUserName": "天乡"},{"transportUser": 473, "transportUserName": "天乡"},{"transportUser": 475, "transportUserName": "天乡"}]
```

> sql是这样写的

```sql
SELECT * FROM your_table
WHERE JSON_CONTAINS(your_json_column, '{"transportUser": 474}')
   OR JSON_CONTAINS(your_json_column, '{"transportUser": 475}');

-- 或者
SELECT * FROM your_table
WHERE JSON_CONTAINS(your_json_column, '{"transportUser": 474}', '$')
   OR JSON_CONTAINS(your_json_column, '{"transportUser": 475}', '$');
```

## 临时变量的使用

> @这个生成的是全局变量，使用场景要小心，因为这个数据是会被共用的

>业务中，select中的字段，如果下一个字段需要用到上一个字段的值的时候是不可以直接获取的

*错误代码如下：*

```sql
select ifnull((select count(id) from sale_outbound_item_bolt where so.id = outboundId),0) as pipeNum,
ifnull((select sum(soib_yard.yardQuantity) from sale_outbound_item_bolt soib_yard where so.id = soib_yard.outboundId),0) as yardQuantity,
format(ifnull((select sum(soib_yard.yardQuantity) from sale_outbound_item_bolt soib_yard where so.id = soib_yard.outboundId),0) * 0.9144,6) as meterQuantity,
format(meterQuantity * ifnull(soi.orderPurchasePrice,0),2) as amount,
amount as orderAmount,
amount as orderReceivable
from sale_outbound so
inner join sale_outbound_item soi on so.id = soi.outboundId
```

>如上sql，所示：meterQuantity取值，是取的上一个字段的。但是select中直接这样取是会报错的。所以需要通过临时变量，来获取数据。

*正确用法如下：*

>通过 `@` 符号来定临时变量，将原来的字段，赋值给临时变量，这样下一个字段才能获取到上一个字段。

```sql
select ifnull((select count(id) from sale_outbound_item_bolt where so.id = outboundId),0) as pipeNum,  
ifnull((select sum(soib_yard.yardQuantity) from sale_outbound_item_bolt soib_yard where so.id = soib_yard.outboundId),0) as yardQuantity,  
@temporaryMeterQuantity := format(ifnull((select sum(soib_yard.yardQuantity) from sale_outbound_item_bolt soib_yard where so.id = soib_yard.outboundId),0) * 0.9144,6) as meterQuantity,  
@temporaryAmount := format(@temporaryMeterQuantity * ifnull(soi.orderPurchasePrice,0),2) as amount,  
@temporaryAmount as orderAmount,  
@temporaryAmount as orderReceivable  
from sale_outbound so  
inner join sale_outbound_item soi on so.id = soi.outboundId
```

## 将字段json数组的字段，压平

>将color字段，由多个颜色一条数据，打成多条数据


![[Pasted image 20240523174452.png]]

*sql如下：*

```sql
-- 例子
select p.id, p.name, p.code, c.col as color, p.onShelf, p.unit, p.devType, p.images
from product p
left join json_table(p.color, '$[*]' columns (col varchar(50) path '$')) as c on true
where p.id = 128;

-- 核心sql是：
json_table(p.color, '$[*]' columns (col varchar(50) path '$')) as c on true
```

*结果如下图所示：*

![[Pasted image 20240523175131.png]]

## 当业务中出现要补充时间需要怎么处理呢

> 例如：要补充月份或者补充日期的处理，业务显示如下。
> 为什么要这样处理呢？原因是数据库表时间字段是通过业务数据汇总的，但是有可能某个时间里没有业务产生导致数据库里没有那个时间段的时间。但是业务统计表要显示那个时间段的数据，没有数据怎么按0补充，填补空缺（只有在统计表需要显示，不需要插入表中）。

 **业务如下图：**

![[Pasted image 20240628170648.png]]

> 首先我们需要一个日期生成的sql
> 使用CTE的形式生成（但是仅仅是mysql8.0版本才支持的函数）

```sql
WITH RECURSIVE  
    months AS (SELECT from_unixtime(1704038400000 / 1000, '%Y-%m-%d') AS monthStartDate  
    UNION ALL  
    SELECT DATE_ADD(monthStartDate, INTERVAL 1 MONTH)  
    FROM months  
    WHERE DATE_ADD(monthStartDate, INTERVAL 1 MONTH) <=  
    from_unixtime(1709308799999 / 1000, '%Y-%m-%d'))  
select monthStartDate from  months;
```
>结果如下：

![[Pasted image 20240628171227.png]]

> 数据笛卡尔积的实现，为什么要这么用呢？原因是：比如你拿了这个时间去连表，但是页面上面又要显示一个客户需要多个日期展示的时候，单纯日期去连表就显然不够，需要对客户和日期做一个笛卡尔积。可以通过`CROSS JOIN`函数来实现
> 代码实现如下：

```sql
CROSS JOIN
```
> 整个业务的代码如下：

```sql
WITH RECURSIVE  
    months AS (SELECT from_unixtime(1704038400000 / 1000, '%Y-%m-%d') AS monthStartDate  
               UNION ALL  
               SELECT DATE_ADD(monthStartDate, INTERVAL 1 MONTH)  
               FROM months  
               WHERE DATE_ADD(monthStartDate, INTERVAL 1 MONTH) <=  
                     from_unixtime(1709308799999 / 1000, '%Y-%m-%d')),  
    customer_months AS (SELECT c.id                                  AS customerId,  
                               c.tenantId,  
                               DATE_FORMAT(m.monthStartDate, '%Y%m') AS temporaryMonthDate  
                        FROM maiye_saas_platform_basic.customer c  
                                 CROSS JOIN months m),  
    customer_stats AS (SELECT cm.customerId,  
                              cm.tenantId,  
                              cm.temporaryMonthDate    AS monthDate,  
                              IFNULL(SUM(d.metre), 0)  AS metre,  
                              IFNULL(SUM(d.yard), 0)   AS yard,  
                              IFNULL(SUM(d.amount), 0) AS amount  
                       FROM customer_months cm  
                                INNER JOIN stat_customer_out_refund_day d ON cm.customerId = d.customerId AND  
                                                                             cm.temporaryMonthDate =  
                                                                             d.monthDate and d.tenantId = 19 and  
                                                                             d.dayTimeStamp between 1704038400000 and 1709308799999  
                       GROUP BY cm.customerId, cm.temporaryMonthDate)  
SELECT c.tenantId                                                             tenantId,  
       c.id                                                                   customerId,  
       c.code                                                                 customerCode,  
       c.`name`                                                               customerName,  
       c.clientArea                                                           customerArea,  
       c.customerCreditTypeId                                                 customerCreditTypeId,  
       c.salesmanId                                                           salesmanId,  
       c.orgId                                                                orgId,  
       c.storeId                                                              storeId,  
       GROUP_CONCAT(CONCAT_WS('|', ifnull(cs.monthDate, 197001), ifnull(cs.metre, 0), ifnull(cs.yard, 0),  
                              ifnull(cs.amount, 0)) ORDER BY cs.monthDate) AS detailsStr,  
       row_number() over (order by c.tenantId,c.id)                           number  
FROM maiye_saas_platform_basic.customer c  
         INNER JOIN customer_stats cs ON c.id = cs.customerId  
WHERE c.tenantId = 19  
  and c.potential = 0  
GROUP BY c.id;
```
>detailsStr字段是用来将数据合并串联的，整个月份都是用逗号隔开，里面的不同数据是通过 | 字符隔开。
>效果如下：

![[Pasted image 20240628172101.png]]

## group_concat如果需要去重

> 例子可以在group_concat里面加上distinct去去重

```sql
group_concat(distinct concat_ws('|',u.warehouseId, replace(u.warehouseName, ',', ' '),  
u.yesterdayQuantity,  
u.quantityChange, u.quantity)  
ORDER BY u.warehouseId  
desc) AS detailsStr
```