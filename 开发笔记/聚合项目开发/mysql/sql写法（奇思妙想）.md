## 批量update的sql写法

> 比如需要批量更新，单挑更新是很慢的。

```sql
UPDATE table
SET 
    `activitySort` = CASE id
        WHEN 1 THEN 10
        WHEN 2 THEN 20
    END
WHERE id IN (1, 2)
```


## 窗口函数

> 比如要用到需要将数据标记数字或者排序的时候可以使用这个


```sql
select 
row_number() over (partition by 字段 order by 字段 asc) as rn
from table
```


## 更新插入

> 更新数据时，数据库没有数据则插入数据


```sql
INSERT INTO table_name (col1, col2, col3, ...)
VALUES 
  (val1, val2, val3, ...),
  (val1b, val2b, val3b, ...)
ON DUPLICATE KEY UPDATE
  col2 = VALUES(col2),
  col3 = VALUES(col3);
```

## 生成虚拟列

> 当我们业务中需要增加一个列来加索引，但是这个字段又是没有代码赋值的，我们又需要它是实时同步生成的数据，我们可以通过虚拟列的形式生成。
> 虚拟列：无需手动维护，数据实时同步，VIRTUAL列不占用额外存储空间，可以为虚拟列创建索引。
> json字段也是可以使用这个形式生成新字段的。

```mysql
-- 添加虚拟生成列
ALTER TABLE existing_table 
ADD COLUMN create_timestamp BIGINT GENERATED ALWAYS AS (UNIX_TIMESTAMP(create_datetime)) VIRTUAL;

-- 添加索引
CREATE INDEX idx_create_timestamp ON existing_table (create_timestamp);

-- json的虚拟列
ALTER TABLE table_name
ADD COLUMN c_id VARCHAR(50) GENERATED ALWAYS AS (JSON_UNQUOTE(JSON_EXTRACT(biz_data, '$.cId'))) VIRTUAL;

ALTER TABLE table_name
ADD INDEX idx_c (c_id);
```
