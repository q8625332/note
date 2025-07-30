## update的sql写法


```sql
UPDATE table
SET 
    `activitySort` = CASE id
        WHEN 1 THEN 10
        WHEN 2 THEN 20
    END
WHERE id IN (1, 2)
```
