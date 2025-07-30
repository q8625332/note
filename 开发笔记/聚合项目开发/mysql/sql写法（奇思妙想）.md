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
