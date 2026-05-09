# Codex Configuration

## 中文

Codex 需要通过 Responses API 调用代理，所以关键字段是：

```toml
wire_api = "responses"
base_url = "http://YOUR_SERVER_IP:3002/v1"
env_key = "QWEN_PROXY_API_KEY"
```

完整示例：

```toml
model_provider = "qwen_proxy"
model = "gpt-5.2"
model_reasoning_effort = "high"
web_search = "disabled"

[model_providers.qwen_proxy]
name = "Qwen Proxy"
wire_api = "responses"
base_url = "http://YOUR_SERVER_IP:3002/v1"
env_key = "QWEN_PROXY_API_KEY"
```

PowerShell 临时设置：

```powershell
$env:QWEN_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

Linux / macOS 临时设置：

```bash
export QWEN_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

如果 Codex 的模型选择界面不显示 `qwen3.6-plus`，这是正常现象。可以使用代理暴露出来的别名，例如 `gpt-5.2`。代理会把它转发成真实上游模型。

## English

Codex must call this proxy through the Responses API. The important fields are:

```toml
wire_api = "responses"
base_url = "http://YOUR_SERVER_IP:3002/v1"
env_key = "QWEN_PROXY_API_KEY"
```

Full example:

```toml
model_provider = "qwen_proxy"
model = "gpt-5.2"
model_reasoning_effort = "high"
web_search = "disabled"

[model_providers.qwen_proxy]
name = "Qwen Proxy"
wire_api = "responses"
base_url = "http://YOUR_SERVER_IP:3002/v1"
env_key = "QWEN_PROXY_API_KEY"
```

Temporary PowerShell environment variable:

```powershell
$env:QWEN_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

Temporary Linux / macOS environment variable:

```bash
export QWEN_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

If the Codex model picker does not show `qwen3.6-plus`, that is expected. Use a proxy alias such as `gpt-5.2`; the proxy will rewrite it to the real upstream model.
