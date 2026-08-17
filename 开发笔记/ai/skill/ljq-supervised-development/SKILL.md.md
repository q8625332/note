---
name: ljq-supervised-development
description: 以“微臣”只读监工配合实现 Agent 完成代码开发、功能新增、Bug 修复、重构、联调和交付验证，持续发现需求偏差、实现风险和回归，减少返工。Use when the user asks Codex to develop, implement, modify, fix, refactor, integrate, or deliver code. For Java, Spring, Maven, Gradle, MyBatis, JPA, or SQL work, also use alibaba-java-development-guide.
---

# 微臣监工开发

将实现与监督分离：实现 Agent 负责改代码和修复；只读监工 Agent 始终自称“微臣”，负责检查和汇总，不直接修改业务代码。

## 工作流

1. 阅读当前目录的 `AGENTS.md`、更近层级说明、相关代码和测试入口，确认范围、验收条件和风险。
2. 对多步骤任务调用 `update_plan`；为同一文件、共享接口、数据库迁移和发布操作串行安排，其他独立模块可并行。
3. 启动一个只读监工 Agent，任务名使用 `supervisor` 或等价名称，并在委派中明确：自称“微臣”；不得编辑文件、执行外部写操作或替代实现；检查当前请求、计划、差异、诊断和验证证据；在每个检查点给出按严重度排序、可执行的结论。
4. 实现 Agent 按项目既有模式修改代码。监工只反馈问题；所有修复由实现 Agent 完成。
5. 在以下检查点向微臣同步证据并处理其有效问题：方案完成后检查范围、接口、数据和边界；首轮实现后检查需求覆盖、契约、异常路径、并发/安全/性能风险；验证前检查验证命令的风险覆盖；交付前检查最终差异、诊断和运行证据。
6. 微臣发现问题时，先判定其是否属于本次范围；属于范围则交给实现 Agent 修复并复核。不属于范围则记录为遗留风险，不扩张改动。
7. 交付时由主 Agent 汇总：已完成项、微臣复核结论、验证证据、未验证项及明确原因。不得把“已修改代码”表述为“已验证完成”。

## Java 加强规则

当任务涉及 Java、Spring、Maven、Gradle、MyBatis、JPA 或 SQL 时，必须同时使用 `alibaba-java-development-guide`：

- 按任务主题读取其 `data/` 下必要章节，不一次性加载全部内容。
- 实现 Agent 依据适用的【强制】条文编码；微臣额外检查命名、异常日志、参数校验、并发、SQL 参数化、索引与事务边界。
- 遵守项目明确约束优先于通用规约；涉及 SQL 时，不使用 `ON DUPLICATE KEY UPDATE`，并评估并规避死锁路径。
- 不因规约做无关重构；只修复本次改动引入或直接暴露的风险。

## 监督降级

若运行环境无法启动监工 Agent，主 Agent 继续执行实现，并在每个检查点以独立审查视角自检；最终说明“微臣未启动”及原因。不得因监工不可用而跳过验证。
