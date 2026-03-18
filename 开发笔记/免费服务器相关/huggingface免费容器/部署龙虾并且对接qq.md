
首先创建一个docker项目

然后在设置中配置要用到的数据。

## Variables and secrets

### Variables


```
// 网关去除校验
key: OPENCLAW_GATEWAY_CONTROLUI_DANGEROUSLYDISABLEDEVICEAUTH
value: true

// ai模型地址
key: OPENCLAW_MODEL_BASE_URL
value: 模型地址

// 模型id
key: OPENCLAW_MODEL_ID
value: gpt-5.2-codex

// 模型类型，不填
key: OPENCLAW_MODEL_API_TYPE
value: 

// 常用头
key: OPENCLAW_MODEL_USER_AGENT
value: Mozilla/5.0

// qq app id
key: QQBOT_APPID
value: ###

// qq app secret
key: QQBOT_APPSECRET
value: ###

```

### Secrets


```
// 网关token，记住这个是要登录用的
key: OPENCLAW_GATEWAY_TOKEN
value: 

// ai 调用时要用的key
key: MYMODEL_API_KEY
value: 

```

获取qqbot密钥：[使用 QQBot接入 OpenClaw 的”养虾”指南](https://docs.qq.com/doc/DS2FmdkJZZEJJWEFF?no_promotion=1&is_blank_or_template=blank)

[q.qq.com](https://q.qq.com/qqbot/openclaw/login.html)

## 创建部署文件。

## Dockerfile


```
FROM ghcr.io/1186258278/openclaw-zh:nightly

# 设置环境变量
ENV OPENCLAW_STATE_DIR=/data/.openclaw \
    OPENCLAW_CONFIG_PATH=/data/.openclaw/openclaw.json \
    OPENCLAW_SKIP_CANVAS_HOST=1

WORKDIR /app

# === 修改开始：安装 Python3 和 pip ===
# 基础镜像基于 Debian/Ubuntu，使用 apt-get 安装
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*
# === 修改结束 ===

# 复制启动脚本和模板配置
COPY start.sh /start.sh
COPY openclaw.json /template_openclaw.json

# 安装 OpenClaw QQBot 插件
RUN openclaw plugins install @tencent-connect/openclaw-qqbot@latest || true

# 赋予启动脚本执行权限
RUN chmod +x /start.sh

EXPOSE 7860

CMD ["/start.sh"]
```

## README.md


```
---
title: OpenClaw 
emoji: 🦀
colorFrom: indigo
colorTo: blue
sdk: docker
pinned: false
license: mit
---

```


## openclaw.json


```
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "mymodel/gpt-5.2-codex"
      }
    }
  },
  "models": {
    "mode": "merge",
    "providers": {
      "mymodel": {
        "baseUrl": "https://chenyehua-ljq-ccload.hf.space/v1",
        "apiKey": "${OPENCLAW_MODEL_API_KEY}",
        "api": "openai-completions",
        "models": [
          {
            "id": "gpt-5.2-codex",
            "name": "gpt5.2codex"
          }
        ]
      }
    }
  },
  "gateway": {
    "controlUi": {
      "allowedOrigins": [
        "https://chenyehua-openclaw-ljq.hf.space"
      ]
    }
  }
}
```


## start.sh


```
#!/bin/sh
set -eu

PORT="${PORT:-7860}"
STATE_DIR="${OPENCLAW_STATE_DIR:-/data/.openclaw}"
CONFIG_PATH="${OPENCLAW_CONFIG_PATH:-/data/.openclaw/openclaw.json}"

MODEL_PROVIDER="${OPENCLAW_MODEL_PROVIDER:-}"
MODEL_ID="${OPENCLAW_MODEL_ID:-gpt-5.2-codex}"
MODEL_NAME="${OPENCLAW_MODEL_NAME:-}"
MODEL_BASE_URL="${OPENCLAW_MODEL_BASE_URL:-https://chenyehua-ljq-ccload.hf.space}"
MODEL_API_TYPE="${OPENCLAW_MODEL_API_TYPE:-openai-completions}"
MODEL_API_KEY="${OPENCLAW_MODEL_API_KEY:-${MYMODEL_API_KEY:-}}"
MODEL_USER_AGENT="${OPENCLAW_MODEL_USER_AGENT:-}"
HF_ORIGIN="${OPENCLAW_ALLOWED_ORIGIN:-https://chenyehua-openclaw-ljq.hf.space}"

QQBOT_APP_ID_EFFECTIVE="${QQBOT_APP_ID:-${QQBOT_APPID:-}}"
QQBOT_APP_SECRET_EFFECTIVE="${QQBOT_APP_SECRET:-${QQBOT_APPSECRET:-${QQBOT_SECRET:-}}}"
QQBOT_EXTRA_ACCOUNTS="${QQBOT_EXTRA_ACCOUNTS:-}"

mkdir -p "$STATE_DIR"
mkdir -p "$(dirname "$CONFIG_PATH")"

if [ ! -f "$CONFIG_PATH" ]; then
    echo "Initializing OpenClaw config..."
    cp /template_openclaw.json "$CONFIG_PATH"
fi

if [ -n "${MODEL_API_KEY:-}" ]; then
    export OPENCLAW_MODEL_API_KEY="${MODEL_API_KEY}"
fi

export CONFIG_PATH MODEL_PROVIDER MODEL_ID MODEL_NAME MODEL_BASE_URL MODEL_API_TYPE MODEL_USER_AGENT HF_ORIGIN
python3 <<'PY'
import json
import os
from pathlib import Path

config_path = Path(os.environ["CONFIG_PATH"])
provider = os.environ.get("MODEL_PROVIDER", "").strip().lower()
model_id = os.environ["MODEL_ID"]
model_name = os.environ.get("MODEL_NAME", "").strip() or model_id.split("/")[-1]
base_url = os.environ["MODEL_BASE_URL"]
api_type = os.environ.get("MODEL_API_TYPE", "openai-completions").strip() or "openai-completions"
user_agent = os.environ.get("MODEL_USER_AGENT", "").strip()

api_type_alias = {
    "openai": "openai-completions",
    "openai-chat": "openai-completions",
    "responses": "openai-responses",
    "openai-resp": "openai-responses",
    "anthropic": "anthropic-messages",
    "claude": "anthropic-messages",
    "google": "google-generative-ai",
    "gemini": "google-generative-ai",
}
api_type = api_type_alias.get(api_type, api_type)

base_url = base_url.rstrip("/")
if api_type in ("openai-completions", "openai-responses"):
    if base_url.endswith("/v1") or "/v1/" in base_url:
        normalized_base_url = base_url
    else:
        normalized_base_url = f"{base_url}/v1"
else:
    normalized_base_url = base_url

if not provider:
    provider = "anthropic" if api_type == "anthropic-messages" else "mymodel"

hf_origin = os.environ["HF_ORIGIN"]
model_ref = f"{provider}/{model_id}"

try:
    data = json.loads(config_path.read_text(encoding="utf-8"))
except Exception:
    data = {}

agents = data.setdefault("agents", {})
defaults = agents.setdefault("defaults", {})
defaults["model"] = {"primary": model_ref}

models = data.setdefault("models", {})
models["mode"] = "merge"
providers = models.setdefault("providers", {})
provider_config = {
    "baseUrl": normalized_base_url,
    "apiKey": "${OPENCLAW_MODEL_API_KEY}",
    "api": api_type,
    "models": [{"id": model_id, "name": model_name}],
}
if user_agent:
    provider_config["headers"] = {"User-Agent": user_agent}
providers[provider] = provider_config

gateway = data.setdefault("gateway", {})
control_ui = gateway.setdefault("controlUi", {})
origins = control_ui.get("allowedOrigins", [])
if not isinstance(origins, list):
    origins = []
if hf_origin not in origins:
    origins.append(hf_origin)
control_ui["allowedOrigins"] = origins
control_ui["dangerouslyAllowHostHeaderOriginFallback"] = True

if os.environ.get("OPENCLAW_GATEWAY_CONTROLUI_DANGEROUSLYDISABLEDEVICEAUTH") == "true":
    control_ui["dangerouslyDisableDeviceAuth"] = True

config_path.write_text(json.dumps(data, ensure_ascii=False, indent=2) + "\n", encoding="utf-8")
PY

if [ -z "${OPENCLAW_MODEL_API_KEY:-}" ]; then
    echo "Warning: OPENCLAW_MODEL_API_KEY or MYMODEL_API_KEY not set. Model provider calls will fail."
fi

echo "Ensuring latest qqbot plugin installed..."
if [ -d "/data/.openclaw/extensions/openclaw-qqbot" ]; then
    echo "QQBot plugin already installed, skipping."
else
    openclaw plugins install @tencent-connect/openclaw-qqbot@latest || echo "Warning: qqbot plugin install failed, continue."
fi

export CONFIG_PATH QQBOT_APP_ID_EFFECTIVE QQBOT_APP_SECRET_EFFECTIVE QQBOT_EXTRA_ACCOUNTS
python3 <<'PY'
import json
import os
from pathlib import Path

config_path = Path(os.environ["CONFIG_PATH"])
app_id = os.environ.get("QQBOT_APP_ID_EFFECTIVE", "").strip()
app_secret = os.environ.get("QQBOT_APP_SECRET_EFFECTIVE", "").strip()
extra_accounts_raw = os.environ.get("QQBOT_EXTRA_ACCOUNTS", "").strip()

try:
    data = json.loads(config_path.read_text(encoding="utf-8"))
except Exception:
    data = {}

channels = data.setdefault("channels", {})
qqbot = channels.setdefault("qqbot", {})

if app_id and app_secret:
    qqbot["enabled"] = True
    qqbot["appId"] = app_id
    qqbot["clientSecret"] = app_secret

if extra_accounts_raw:
    try:
        extra_accounts = json.loads(extra_accounts_raw)
        if isinstance(extra_accounts, dict):
            accounts = qqbot.setdefault("accounts", {})
            for name, conf in extra_accounts.items():
                if not isinstance(conf, dict):
                    continue
                item = accounts.setdefault(name, {})
                if "enabled" not in conf:
                    item["enabled"] = True
                for k in ("enabled", "appId", "clientSecret"):
                    if k in conf and conf[k] is not None:
                        item[k] = conf[k]
        else:
            print("Warning: QQBOT_EXTRA_ACCOUNTS is not a JSON object, ignored.")
    except Exception as e:
        print(f"Warning: failed to parse QQBOT_EXTRA_ACCOUNTS: {e}")

plugins = data.setdefault("plugins", {})
allow = plugins.setdefault("allow", [])
if "openclaw-qqbot" not in allow:
    allow.append("openclaw-qqbot")

config_path.write_text(json.dumps(data, ensure_ascii=False, indent=2) + "\n", encoding="utf-8")
PY

if [ -n "${QQBOT_APP_ID_EFFECTIVE}" ] && [ -n "${QQBOT_APP_SECRET_EFFECTIVE}" ]; then
    echo "QQBot credentials written to config."
else
    echo "Warning: QQBOT credentials not set. Skipping default QQBot configuration."
fi

echo "Cleaning broken built-in extension directories..."
python3 <<'PY'
from pathlib import Path
import shutil

root = Path("/app/dist/extensions")
if root.exists():
    removed = []
    for d in root.iterdir():
        if d.is_dir():
            manifest = d / "openclaw.plugin.json"
            if not manifest.exists():
                removed.append(str(d))
                shutil.rmtree(d, ignore_errors=True)
    if removed:
        print("Removed broken extension dirs:")
        for x in removed:
            print(" -", x)
    else:
        print("No broken built-in extension directories found.")
else:
    print("/app/dist/extensions does not exist, skip cleanup.")
PY

echo "Starting OpenClaw Gateway (run) on port ${PORT}"
exec openclaw gateway run \
  --bind lan \
  --port "${PORT}" \
  ${OPENCLAW_GATEWAY_TOKEN:+--token "${OPENCLAW_GATEWAY_TOKEN}"} \
  --allow-unconfigured
```
