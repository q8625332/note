---
name: ljq-sql-audit
description: Use when Codex writes, changes, reviews, or optimizes MySQL SQL in application code, Mapper/XML, migrations, DDL, DAO/repository code, or production change scripts; audits slow SQL, indexes, EXPLAIN plans, transaction locks, lock waits, and deadlock risk, and blocks ON DUPLICATE KEY UPDATE before code is delivered.
---

# LJQ SQL Audit

## Core rule

Audit every new or changed SQL statement before delivery. Correct business and concurrency semantics come first; then minimize scanned rows, locked rows, lock duration, and indexes. Never write or approve `ON DUPLICATE KEY UPDATE`.

Read [references/mysql-audit-rules.md](references/mysql-audit-rules.md) before auditing SQL, proposing an index, or interpreting `EXPLAIN`.

## Required workflow

1. Find every changed SQL statement, including annotations, string builders, Mapper/XML, migrations, stored procedures, and generated SQL.
2. Recover the real data path: caller, transaction boundary, write order, table DDL, primary/unique keys, existing indexes, row count or cardinality, and expected result size.
3. Run `python scripts/audit_sql.py <changed-files>` from this Skill directory. Treat every `ERROR` as a delivery blocker. Review every `WARN`; do not suppress one without code, schema, or plan evidence. Script `PASS` is only the first gate, never the final SQL audit verdict.
4. Reject `ON DUPLICATE KEY UPDATE`. Do not mechanically replace it. Preserve insert/update, idempotency, affected-row, retry, and concurrent-insert semantics with an explicit flow.
5. Audit lock behavior: rows and index ranges scanned by every `UPDATE`, `DELETE`, `SELECT ... FOR UPDATE`, foreign-key check, and multi-table transaction. Require selective indexes, a consistent lock order, short transactions, and whole-transaction deadlock retry.
6. When a database connection is available, collect `SELECT VERSION()`, `SHOW CREATE TABLE`, `SHOW INDEX`, approximate table size/cardinality, and `EXPLAIN FORMAT=JSON` or `EXPLAIN FORMAT=TREE`. Never infer runtime plans from checked-in DDL alone.
7. Rewrite SQL or propose the smallest useful index. Compare the plan before and after. Do not add an index already covered by an existing leftmost prefix, and account for write/storage cost.
8. If the task authorizes index changes and the environment is local, development, or test, execute the compatible index DDL when permissions allow, then rerun `SHOW INDEX` and `EXPLAIN`. For production or an unknown environment, output the DDL and rollback DDL but require explicit confirmation before execution.
9. Report the verdict and evidence using the contract below. Do not call SQL safe or fast without current evidence.

## Database safety

- Use plain `EXPLAIN` for write statements; it does not execute the described DML.
- Use `EXPLAIN ANALYZE` only for a confirmed read-only `SELECT` in a safe environment. It executes the query.
- Do not run `ANALYZE TABLE`, DDL, load tests, locking reads, or write statements against production or an unknown environment without explicit approval.
- Before adding an index, identify MySQL version, table engine, table size, replica/online-DDL constraints, duplicate indexes, and the supported `ALGORITHM`/`LOCK` options.
- After adding an index, verify that it exists and that the target plan actually uses it. Permission success alone is not optimization evidence.

## Output contract

Return these fields in order:

```text
结论: PASS | BLOCKED | NEEDS_DB_EVIDENCE
阻断项: file:line + rule + reason
SQL/索引改动: original -> rewritten; executed DDL or proposed DDL
EXPLAIN 前后: access type, key, estimated/actual rows, filtered, Extra/iterator timing
锁与死锁: locked rows/ranges, transaction order, retry behavior
数据库变更汇总: environment, table, applied DDL, rollback DDL, verification
未验证项: missing connection/schema/statistics/runtime evidence
```

`PASS` requires no forbidden construct, preserved behavior, and evidence proportionate to the table and concurrency risk. If a relevant database is unavailable, use `NEEDS_DB_EVIDENCE`, provide exact commands/DDL, and state what remains unverified.

## Example

For a daily counter with unique key `(user_id, stat_date)`, reject a one-line upsert. Inspect whether the row is pre-created and how duplicate insert races are handled. A valid alternative may be `UPDATE` first, then a conditional `INSERT`, with duplicate-key recovery retrying the `UPDATE`; the exact flow must match the surrounding transaction and affected-row contract. Verify the unique key and both paths instead of assuming them.

## Red flags

- "One statement is more atomic, so the forbidden upsert is acceptable."
- "The table is small today, so a full scan is acceptable in permanent code."
- "An index exists on one filter column, so the query is indexed."
- "`EXPLAIN` chose a key, so the plan is fast."
- "Database permission means production DDL is approved."
- "Deadlocks are rare, so retry and lock ordering are unnecessary."

Any red flag means stop, gather evidence, and redesign or report `BLOCKED` / `NEEDS_DB_EVIDENCE`.
