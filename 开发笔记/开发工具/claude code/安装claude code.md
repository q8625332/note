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

或者手动打开

```win
// 打开系统文件
C:\Users\%username%\.claude
```

**settings.json**

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


## 使用ccr启动


```
npm install -g @musistudio/claude-code-router
```

配置文件： `~/.claude-code-router/config.json`  
_(如果没有就创建)_

配置内容示例：


```
{
  "Providers": [
    {
      "name": "gpt-load-openai",
      "api_base_url": "http://localhost:3001/proxy/openai/v1/chat/completions",
      "api_key": "sk-123456",
      "models": [
        "gpt-4.1-mini",
        "gpt-4.1-nano"
      ]
    },
    {
      "name": "gpt-load-gemini",
      "api_base_url": "http://localhost:3001/proxy/gemini/v1beta/models/",
      "api_key": "sk-123456",
      "models": [
        "gemini-2.5-pro",
        "gemini-2.5-flash"
      ],
      "transformer": {
        "use": [
          "gemini"
        ]
      }
    },
    {
      "name": "gpt-load-gemini-openai",
      "api_base_url": "http://localhost:3001/proxy/gemini/v1beta/openai/chat/completions",
      "api_key": "sk-123456",
      "models": [
        "gemini-2.5-pro",
        "gemini-2.5-flash"
      ]
    },
    {
      "name": "gpt-load-anthropic",
      "api_base_url": "http://localhost:3001/proxy/anthropic/v1/messages",
      "api_key": "sk-123456",
      "models": [
        "claude-sonnet-4-20250514",
        "claude-3-haiku-20240307"
      ],
      "transformer": {
        "use": [
          "Anthropic"
        ]
      }
    }
  ],
  "Router": {
    "default": "gpt-load-gemini,gemini-2.5-pro",
    "background": "gpt-load-gemini,gemini-2.5-flash",
    "think": "gpt-load-gemini,gemini-2.5-pro",
    "longContext": "gpt-load-gemini,gemini-2.5-pro",
    "longContextThreshold": 60000,
    "webSearch": "gpt-load-gemini,gemini-2.5-flash"
  }
}
```

**说明：**

- 上面的 `gpt-load-gemini-openai` 渠道是Gemini的OpenAI兼容格式。
- 记得把 `http://localhost:3001`，还有路径里面的分组名称（`openai`,`gemini`）换成自己的。
- `Providers` 里面的渠道配置，根据自己 `GPT-Load` 的分组渠道自行调整。没有就删除。
- `sk-123456` 密钥也要改成自己的代理密钥。
- 还有个细节，anthropic渠道里面的transformer.use值是：`Anthropic`，首字母一定要大写。
- 模型也要根据自己的渠道配置，我只是写了几个官方默认的。
- **最后重点，`Router`里面我默认配置了gemini的模型，你根据自己需求调整配置。**

**最简单的就是使用ui配置：**

```
ccr ui
```


## 使用

CCR！ 启动！！！


```
ccr code
```
