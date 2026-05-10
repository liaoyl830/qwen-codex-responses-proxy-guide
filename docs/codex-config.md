# Codex Configuration

## 中文

Codex 需要通过 Responses API 调用代理，所以关键字段是：

```toml
wire_api = "responses"
base_url = "BASE_URL"
env_key = "CHINA_LLM_PROXY_API_KEY"
```

`BASE_URL` 按部署方式选择：

```text
云服务器版：http://YOUR_SERVER_IP:3002/v1 或 https://api.example.com/v1
Windows / macOS 本机版：http://127.0.0.1:3002/v1
```

完整示例：

```toml
model_provider = "china_llm_proxy"
model = "gpt-5.2"
web_search = "disabled"

[model_providers.china_llm_proxy]
name = "China LLM Proxy"
wire_api = "responses"
base_url = "BASE_URL"
env_key = "CHINA_LLM_PROXY_API_KEY"
```

PowerShell 临时设置：

```powershell
$env:CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

Linux / macOS 临时设置：

```bash
export CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

如果 Codex 的模型选择界面不显示 `qwen3.6-plus`、`deepseek-chat`、`MiniMax-M2.7` 或 `glm-4.7`，这是正常现象。可以使用代理暴露出来的别名，例如 `gpt-5.2`。代理会把它转发成真实上游模型。

你可以在 `config.yaml` 里把 `gpt-5.2` 映射到任何一个上游模型。对 Codex 来说，模型名保持不变；对代理来说，真实上游可以是 Qwen、DeepSeek、MiniMax、GLM 或其他 OpenAI 兼容模型。

`model_reasoning_effort` 不写进通用示例。不同厂商的思考参数不统一，确认目标模型和代理转换都支持后再自行添加。

## English

Codex must call this proxy through the Responses API. The important fields are:

```toml
wire_api = "responses"
base_url = "BASE_URL"
env_key = "CHINA_LLM_PROXY_API_KEY"
```

Choose `BASE_URL` by deployment mode:

```text
Cloud server: http://YOUR_SERVER_IP:3002/v1 or https://api.example.com/v1
Windows / macOS local desktop: http://127.0.0.1:3002/v1
```

Full example:

```toml
model_provider = "china_llm_proxy"
model = "gpt-5.2"
web_search = "disabled"

[model_providers.china_llm_proxy]
name = "China LLM Proxy"
wire_api = "responses"
base_url = "BASE_URL"
env_key = "CHINA_LLM_PROXY_API_KEY"
```

Temporary PowerShell environment variable:

```powershell
$env:CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

Temporary Linux / macOS environment variable:

```bash
export CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

If the Codex model picker does not show `qwen3.6-plus`, `deepseek-chat`, `MiniMax-M2.7`, or `glm-4.7`, that is expected. Use a proxy alias such as `gpt-5.2`; the proxy will rewrite it to the real upstream model.

You can map `gpt-5.2` to any upstream model in `config.yaml`. Codex keeps the same model name; the proxy can route it to Qwen, DeepSeek, MiniMax, GLM, or another OpenAI-compatible model.

`model_reasoning_effort` is intentionally omitted from the generic example. Reasoning controls differ by provider; add it only after verifying that the target model and proxy translation support it.
