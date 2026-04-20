# 免费 LLM API 统一网关

基于 [LiteLLM](https://litellm.ai/) 的 OpenAI 兼容代理，把多个免费 LLM API 整合成统一接口。

## 支持的免费 Provider

| Provider | 免费额度 | 申请地址 |
|----------|---------|---------|
| Google Gemini | 15 RPM, 100万上下文 | [AI Studio](https://aistudio.google.com/app/apikey) |
| Mistral AI | 10亿 tokens/月 | [Console](https://console.mistral.ai/api-keys) |
| Groq | 30 RPM, 超快推理 | [Console](https://console.groq.com/keys) |
| Cerebras | 30 RPM, 超快推理 | [Cloud](https://cloud.cerebras.ai/) |
| Cohere | 1000次/月 | [Dashboard](https://dashboard.cohere.com/api-keys) |
| OpenRouter | 多个免费模型 | [Settings](https://openrouter.ai/settings/keys) |
| 智谱 AI | GLM-4-Flash 免费 | [开放平台](https://open.bigmodel.cn/usercenter/apikeys) |

## 快速部署

```bash
cd /root/projects/free-llm-gateway

# 1. 编辑 .env，填入你的 API keys
vim .env

# 2. 启动
docker compose up -d

# 3. 测试
curl http://localhost:4000/v1/models \
  -H "Authorization: Bearer sk-free-llm-gateway"
```

## 使用方法

所有请求走 `/v1/chat/completions`，兼容 OpenAI SDK：

```bash
# 用 Groq 的 Llama 3.3
curl http://localhost:4000/v1/chat/completions \
  -H "Authorization: Bearer sk-free-llm-gateway" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "groq/llama-3.3-70b-versatile",
    "messages": [{"role": "user", "content": "Hello!"}],
    "max_tokens": 100
  }'

# 用 Gemini 2.5 Flash
curl http://localhost:4000/v1/chat/completions \
  -H "Authorization: Bearer sk-free-llm-gateway" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemini/gemini-2.5-flash",
    "messages": [{"role": "user", "content": "Hello!"}],
    "max_tokens": 100
  }'
```

## Python 示例

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-free-llm-gateway",
    base_url="http://your-server:4000/v1"
)

# 切换模型只需改 model 名
response = client.chat.completions.create(
    model="groq/llama-3.3-70b-versatile",  # 或 gemini/gemini-2.5-flash
    messages=[{"role": "user", "content": "Say hi"}],
    max_tokens=50
)
print(response.choices[0].message.content)
```

## 接入 Hermes

在 `~/.hermes/config.yaml` 添加 provider：

```yaml
custom_providers:
  - name: free-gateway
    base_url: http://localhost:4000/v1
    api_key: sk-free-llm-gateway
    models:
      - id: gemini/gemini-2.5-flash
        name: Gemini 2.5 Flash
      - id: groq/llama-3.3-70b-versatile
        name: Llama 3.3 70B (Groq)
      - id: mistral/mistral-small-latest
        name: Mistral Small
```

## 查看可用模型

```bash
curl http://localhost:4000/v1/models \
  -H "Authorization: Bearer sk-free-llm-gateway"
```
