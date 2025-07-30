**创建json里某个字段的索引**

> 当我们使用json的查询的时候，没有对应索引就会查询全表，速度很慢。
> 索引需要增加索引，但是又是通过json字段储存的，想要数据不变的情况下，又有新增索引。
> 使用虚拟字段的形式拓展


```sql
ALTER TABLE table
ADD COLUMN user_id VARCHAR(50) GENERATED ALWAYS AS (JSON_UNQUOTE(JSON_EXTRACT(biz_data, '$.userId'))) VIRTUAL;

ALTER TABLE table
ADD INDEX idx_event_action_user (user_id);
```
