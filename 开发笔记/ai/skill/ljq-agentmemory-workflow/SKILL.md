---
name: ljq-agentmemory-workflow
description: Make AgentMemory part of Codex's normal working loop so past lessons are recalled before deep work and durable learnings are saved after the task. Use when the task is non-trivial, touches a repo or repeated workflow, needs prior context, or should leave behind searchable knowledge for future sessions.
---

# LJQ AgentMemory Workflow

## Quick start

先查再做，再沉淀：先用 `memory_smart_search` 检索历史经验；任务完成或出现稳定结论后，用 `memory_save` 写入可复用知识。

## Why

AgentMemory 只在两种情况下真正有价值：开始前减少重复摸索，结束后把有效经验变成以后能搜到的知识。不要把它当聊天记录备份，更不要把猜测、噪音或敏感信息写进去。

## Workflow

1. 先判断任务是否值得用 AgentMemory。满足任一条件就使用：任务不止一句话；涉及仓库、模块、环境、脚本、发布、调试、设计规范、常见报错；用户要求“总结经验”“记住”“检索历史”；你怀疑之前做过类似事。
2. 开工前先 recall。用用户目标、仓库名、模块名、关键报错、工具名、分支名或功能名组合成 1 到 3 组检索词，调用 `memory_smart_search`。只复述工具真实返回的内容；没有命中就继续，不要编造“以前可能做过”。
3. 用 recall 结果收敛方案。优先提取已经验证过的路径、已知坑、安装位置、验证命令、禁止事项和可复用的文件路径；把不稳定或过时的信息当作线索，不当作事实。
4. 过程中只在关键节点 remember：根因已经确认；某条命令、配置或目录被证明有效；形成了稳定流程；发现了容易重复踩的坑；补齐了项目约束。临时状态、纯日志噪音、一次性对话内容不要写。
5. 写入时调用 `memory_save`，并满足四个约束：`content` 写清结论和证据背景；`concepts` 用 2 到 5 个具体、可检索的小写短语；`files` 只填真实路径；`type` 优先用 `workflow`、`bug`、`architecture`、`preference` 或 `fact`。
6. 收尾时再做一次 remember。只沉淀未来还会反复用到的结论，例如安装/发布流程、验证姿势、约束边界、定位方法、根因与修复模式。若没有 durable insight，可以不写。
7. 在多步骤任务里，把 AgentMemory 当作当前 AI 的工作习惯：先 recall，再实施，中间在关键决策处 remember，最终对有效经验做一次清理式沉淀。

## Anti-patterns

WRONG: 一上来不检索，结束后把整段对话、命令流水账、敏感 token、猜测结论全部写进记忆。

RIGHT: 开工前按关键词 recall；完成后只保存“下次检索能直接复用”的稳定经验，并给出具体 concepts。

## Checklist

- 开工前是否先做了与任务相关的 recall。
- 输出的历史结论是否全部来自工具返回，而不是主观补全。
- 保存的内容是否稳定、可复用、无敏感信息、无纯噪音。
- `concepts` 是否具体且以后真的搜得到。
- `files` 是否是真实路径，而不是猜测。

## See also

- `agentmemory:recall`
- `agentmemory:remember`
- `agentmemory:agentmemory-mcp-tools`

## Troubleshooting

如果 `memory_smart_search` 或 `memory_save` 当前不可用，明确说明工具不可用，然后退化为本轮正常执行；不要假装已经检索或写入成功。
