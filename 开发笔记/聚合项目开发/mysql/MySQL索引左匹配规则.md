# MySQL索引左匹配规则

MySQL索引的左匹配规则（Leftmost Prefix Rule）是B+树索引使用中的一个重要原则，它决定了索引如何被有效利用。

## 左匹配规则的核心概念

左匹配规则指的是：**MySQL在使用联合索引（复合索引）时，会从索引的最左列开始向右匹配，直到遇到范围查询（>、<、between、like等）就停止匹配**。

## 具体表现

1. **全列匹配**：当查询条件包含联合索引的所有列并且顺序一致时，索引可以被充分利用。
   ```sql
   -- 假设有索引 (col1, col2, col3)
   SELECT * FROM table WHERE col1 = 1 AND col2 = 2 AND col3 = 3;
   ```

2. **最左前缀匹配**：即使没有使用所有索引列，只要使用了最左的列，索引仍然有效。
   ```sql
   -- 有效
   SELECT * FROM table WHERE col1 = 1;
   -- 也有效
   SELECT * FROM table WHERE col1 = 1 AND col2 = 2;
   ```

3. **中间缺失**：如果跳过了最左列，索引将无法使用。
   ```sql
   -- 无效（不使用索引）
   SELECT * FROM table WHERE col2 = 2;
   -- 也无效
   SELECT * FROM table WHERE col2 = 2 AND col3 = 3;
   ```

4. **范围查询后的列**：遇到范围查询后，其后的索引列无法被使用。
   ```sql
   -- 只有col1和col2使用索引，col3不使用
   SELECT * FROM table WHERE col1 = 1 AND col2 > 2 AND col3 = 3;
   ```

## 特殊情况的左匹配

1. **LIKE语句**：以通配符开头的LIKE查询无法使用索引。
   ```sql
   -- 不使用索引
   SELECT * FROM table WHERE name LIKE '%张';
   -- 使用索引（左匹配）
   SELECT * FROM table WHERE name LIKE '张%';
   ```

2. **函数和表达式**：对索引列使用函数或计算会使索引失效。
   ```sql
   -- 不使用索引
   SELECT * FROM table WHERE YEAR(create_time) = 2023;
   -- 使用索引
   SELECT * FROM table WHERE create_time BETWEEN '2023-01-01' AND '2023-12-31';
   ```

## 优化建议

1. 设计联合索引时，将选择性高的列放在左边
2. 频繁查询的列尽量放在联合索引的左侧
3. 避免在索引列上使用函数或计算
4. 对于长字符串考虑使用前缀索引

理解左匹配规则可以帮助你设计更高效的索引，并编写能够充分利用索引的SQL查询。