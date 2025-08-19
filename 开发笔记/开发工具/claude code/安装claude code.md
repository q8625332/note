**win下安装**


```text
npm install -g @anthropic-ai/claude-code
```

**配置**

> claude是基于git bash运行的
> win还需要配置CLAUDE_CODE_GIT_BASH_PATH


```text
// 打开系统环境配置,添加key和value

CLAUDE_CODE_GIT_BASH_PATH

D:\软件\Git\bin\bash.exe
```

**使用git bash窗口写claude的配置**


```text
vim ~/.claude/settings.json
```


```text
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://freeonlyinlinuxdo.alencryenfo.work",
    "ANTHROPIC_AUTH_TOKEN": "vP7tFHqh3nB0U614m8kuzw2MsajJ9O5l"
  },
  "permissions": {
    "allow": [
      "Bash(*)",
      "LS(*)",
      "Read(*)",
      "Write(*)",
      "Edit(*)",
      "MultiEdit(*)",
      "Glob(*)",
      "Grep(*)",
      "Task(*)",
      "WebFetch(*)",
      "WebSearch(*)",
      "TodoWrite(*)",
      "NotebookRead(*)",
      "NotebookEdit(*)"
    ],
    "deny": []
  },
  "model": "sonnet"
}
```


**运行**

```text
claude
```
