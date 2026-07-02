> CodeGraph 是由独立开发者 Colby McHenry 在 2026 年创建的开源项目，它的主要作用是**将代码库解析成语义知识图谱**，并存储在本地 SQLite 数据库中，让 AI 编程助手（如 Claude Code、Cursor、Codex CLI、OpenCode、Hermes Agent 等）可以直接查询图谱，而无需每次扫描文件

## 安装


```
# macOS / Linux

curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh

# Windows (PowerShell)

irm https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.ps1 | iex
```


## 安装到 cli


```
codegraph install
```


## 项目初始化


```
cd your-project

codegraph init
```


