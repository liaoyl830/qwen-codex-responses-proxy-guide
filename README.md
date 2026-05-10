# China LLMs for Codex Responses Proxy

> 中文说明在前，English guide follows below.

## 中文说明

这个仓库是一份通用部署方案：在云服务器、Windows 本机或 macOS 本机部署一个兼容层，让 Codex Desktop / Codex CLI 通过 OpenAI Responses API 调用国内主流 OpenAI Chat Completions 兼容模型。

已覆盖的典型模型/平台：

- 阿里百炼 / Qwen，例如 `qwen-plus`、`qwen3.6-plus`
- DeepSeek，例如 `deepseek-chat`、`deepseek-reasoner`
- MiniMax，例如 `MiniMax-M2.7`、`MiniMax-M2.5`
- 智谱 GLM，例如 `glm-4.7`、`glm-4.6`、`glm-4.5`
- MiMo，例如 `mimo-v2-pro`、`mimo-v2.5-pro`

核心思路：

```text
Codex Desktop / CLI
  -> go-llm-proxy /v1/responses
  -> 国内模型厂商 /v1/chat/completions 或兼容 Chat Completions 地址
  -> 真实上游模型
```

Codex 侧可以把模型名写成 `gpt-5.2` 这样的别名，代理服务器再把它转发成真实上游模型，例如 `qwen3.6-plus`、`deepseek-chat`、`MiniMax-M2.7` 或 `glm-4.7`。这个别名只是为了让客户端更容易选择和调用，不代表实际请求的是 OpenAI GPT 模型。

## 已确认的兼容入口

| 平台 | OpenAI 兼容 Base URL | 示例模型 |
| --- | --- | --- |
| Qwen / 阿里百炼 | `https://dashscope.aliyuncs.com/compatible-mode/v1` | `qwen-plus`, `qwen3.6-plus` |
| DeepSeek | `https://api.deepseek.com/v1` | `deepseek-chat`, `deepseek-reasoner` |
| MiniMax | `https://api.minimax.io/v1` | `MiniMax-M2.7`, `MiniMax-M2.5` |
| GLM / 智谱 | `https://open.bigmodel.cn/api/paas/v4` | `glm-4.7`, `glm-4.6`, `glm-4.5` |
| MiMo | `https://api.mimo-v2.com/v1` 或服务商提供的 OpenAI 兼容地址 | `mimo-v2-pro`, `mimo-v2.5-pro` |

更多说明见 [Provider Catalog](docs/providers.md)。

## 适用场景

- 你已有国内模型厂商的 API Key。
- 你的上游模型主要提供 Chat Completions 兼容接口。
- 你的 Codex 配置需要 `wire_api = "responses"`。
- 你希望上游 API Key 只保存在代理所在环境里，客户端只拿代理 Key。
- 你想把多个模型统一聚合成一个 Codex 可调用入口。

## 准备条件

- 选择一个运行环境：Linux 云服务器、Windows 电脑或 macOS 电脑。
- 对应环境可用的 Docker 和 Docker Compose。
- 至少一个上游模型 API Key。
- 一个你自己生成的代理访问 Key。
- 云服务器版可选：域名、HTTPS 证书、反向代理。
- Windows / macOS 本机版：推荐安装 Docker Desktop，它自带 Docker Compose。

## 部署方式

你可以选择三种部署方式：

| 方式 | 访问地址 | 适合场景 |
| --- | --- | --- |
| 云服务器版 | `https://api.example.com/v1` 或 `http://YOUR_SERVER_IP:3002/v1` | 多台电脑/手机共用、长期在线、对外提供服务 |
| Windows 本机版 | `http://127.0.0.1:3002/v1` | 只给当前 Windows 电脑上的 Codex 使用 |
| macOS 本机版 | `http://127.0.0.1:3002/v1` | 只给当前 Mac 上的 Codex 使用 |

本机版默认只绑定 `127.0.0.1`，不会主动暴露到局域网。完整步骤见 [Windows / macOS 本机部署](docs/local-desktop.md)。

## 云服务器快速部署

1. 克隆仓库：

```bash
git clone https://github.com/liaoyl830/qwen-codex-responses-proxy-guide.git
cd qwen-codex-responses-proxy-guide
```

2. 复制配置：

```bash
cp config.example.yaml config.yaml
cp .env.example .env
```

3. 编辑 `config.yaml`，保留你要启用的模型，替换这些占位符：

```yaml
YOUR_QWEN_API_KEY
YOUR_DEEPSEEK_API_KEY
YOUR_MINIMAX_API_KEY
YOUR_GLM_API_KEY
YOUR_MIMO_API_KEY
YOUR_PROXY_API_KEY
```

推荐自己生成一个强随机代理 Key，例如：

```bash
openssl rand -hex 32
```

4. 启动服务：

```bash
docker compose up -d
```

默认会把服务暴露到服务器的 `3002` 端口，客户端 Base URL 使用：

```text
http://YOUR_SERVER_IP:3002/v1
```

生产环境建议使用 HTTPS，例如：

```text
https://api.example.com/v1
```

## Windows / macOS 本机快速部署

本机版使用只绑定 localhost 的 Compose 文件：

```bash
docker compose -f docker-compose.local.yml up -d
```

Codex 的 Base URL 使用：

```text
http://127.0.0.1:3002/v1
```

Windows PowerShell：

```powershell
$env:CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

macOS / Linux shell：

```bash
export CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

## 测试代理

先按部署方式选择 Base URL：

```text
云服务器版：http://YOUR_SERVER_IP:3002/v1 或 https://api.example.com/v1
本机版：http://127.0.0.1:3002/v1
```

查看模型列表：

```bash
curl BASE_URL/models \
  -H "Authorization: Bearer YOUR_PROXY_API_KEY"
```

测试 Responses API：

```bash
curl BASE_URL/responses \
  -H "Authorization: Bearer YOUR_PROXY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-5.2",
    "input": "Say hello in one short sentence."
  }'
```

如果你想直接测试某个真实模型名，也可以把 `model` 改成 `deepseek-chat`、`MiniMax-M2.7`、`glm-4.7`、`mimo-v2-pro`、`qwen3.6-plus` 等已经在 `config.yaml` 里暴露的名字。

## Codex 配置示例

把下面配置放到 Codex 的 `config.toml` 里，按部署方式替换 `base_url`：

```text
云服务器版：http://YOUR_SERVER_IP:3002/v1 或 https://api.example.com/v1
本机版：http://127.0.0.1:3002/v1
```

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

启动 Codex 前设置代理 Key：

```bash
export CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

Windows PowerShell：

```powershell
$env:CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

## 模型别名

示例配置里同时提供两类名字：

- `gpt-5.2`：给 Codex 使用的外部别名，默认映射到 Qwen。
- 真实模型名：例如 `qwen3.6-plus`、`deepseek-chat`、`MiniMax-M2.7`、`glm-4.7`。

如果你想把 `gpt-5.2` 改成 DeepSeek、MiniMax 或 GLM，只需要修改 `config.yaml` 里 `name: gpt-5.2` 这一项的 `backend`、`api_key` 和 `model`。

## 安全建议

- 不要把真实 API Key 写进 Git 仓库。
- 云服务器版不要把代理端口裸露给全网长期使用，至少要开启代理 Key。
- 云服务器生产环境建议加 HTTPS；本机版使用 `127.0.0.1` 时通常不需要 HTTPS。
- 如果 Key 泄露，立刻在上游平台和代理配置里轮换。
- 不同厂商的计费、上下文缓存、工具调用和限流策略不同，上线前要用真实工作流压测。
- 这个方案是兼容层，不是 OpenAI、阿里云、DeepSeek、MiniMax、智谱或 MiMo 的官方产品。

更多内容见：

- [Codex 配置](docs/codex-config.md)
- [Provider Catalog](docs/providers.md)
- [参数兼容性](docs/parameter-compatibility.md)
- [Windows / macOS 本机部署](docs/local-desktop.md)
- [官方文档来源](docs/sources.md)
- [安全建议](docs/security.md)
- [常见问题](docs/troubleshooting.md)

## English Guide

This repository provides a generic deployment guide for running a compatibility layer on a cloud server, Windows PC, or Mac. It lets Codex Desktop / Codex CLI call mainstream China-hosted OpenAI Chat Completions compatible models through an OpenAI Responses API compatible endpoint.

Covered provider examples:

- Alibaba Model Studio / Qwen, such as `qwen-plus` and `qwen3.6-plus`
- DeepSeek, such as `deepseek-chat` and `deepseek-reasoner`
- MiniMax, such as `MiniMax-M2.7` and `MiniMax-M2.5`
- Zhipu GLM, such as `glm-4.7`, `glm-4.6`, and `glm-4.5`
- MiMo, such as `mimo-v2-pro` and `mimo-v2.5-pro`

Architecture:

```text
Codex Desktop / CLI
  -> go-llm-proxy /v1/responses
  -> Provider /v1/chat/completions or compatible Chat Completions endpoint
  -> Real upstream model
```

Codex can request an alias such as `gpt-5.2`. The proxy rewrites that external model name to the real upstream model, such as `qwen3.6-plus`, `deepseek-chat`, `MiniMax-M2.7`, or `glm-4.7`. The alias is only a client-facing name; it does not mean the upstream model is an OpenAI GPT model.

## Confirmed Compatible Endpoints

| Provider | OpenAI-compatible Base URL | Example models |
| --- | --- | --- |
| Qwen / Alibaba Model Studio | `https://dashscope.aliyuncs.com/compatible-mode/v1` | `qwen-plus`, `qwen3.6-plus` |
| DeepSeek | `https://api.deepseek.com/v1` | `deepseek-chat`, `deepseek-reasoner` |
| MiniMax | `https://api.minimax.io/v1` | `MiniMax-M2.7`, `MiniMax-M2.5` |
| GLM / Zhipu | `https://open.bigmodel.cn/api/paas/v4` | `glm-4.7`, `glm-4.6`, `glm-4.5` |
| MiMo | `https://api.mimo-v2.com/v1` or your provider's OpenAI-compatible endpoint | `mimo-v2-pro`, `mimo-v2.5-pro` |

See [Provider Catalog](docs/providers.md) for details.

## Use Cases

- You have API keys from one or more China-hosted model providers.
- Your upstream model mainly exposes a Chat Completions compatible API.
- Your Codex provider must use `wire_api = "responses"`.
- You want upstream API keys to stay in the environment where the proxy runs, while clients only use a proxy key.
- You want one Codex-compatible endpoint for multiple upstream models.

## Requirements

- Choose one runtime: Linux cloud server, Windows PC, or Mac.
- Docker and Docker Compose for that runtime.
- At least one upstream model API key.
- A strong proxy API key generated by you.
- Cloud server optional: domain name, HTTPS certificate, and reverse proxy.
- Windows / macOS local: Docker Desktop is the recommended install path and includes Docker Compose.

## Deployment Modes

You can deploy this guide in three modes:

| Mode | Base URL | Best for |
| --- | --- | --- |
| Cloud server | `https://api.example.com/v1` or `http://YOUR_SERVER_IP:3002/v1` | Multiple devices, always-on usage, shared service |
| Windows local desktop | `http://127.0.0.1:3002/v1` | Codex on the same Windows PC |
| macOS local desktop | `http://127.0.0.1:3002/v1` | Codex on the same Mac |

The local desktop mode binds only to `127.0.0.1` by default, so it is not exposed to your LAN. See [Windows / macOS local deployment](docs/local-desktop.md) for full steps.

## Cloud Server Quick Start

1. Clone the repository:

```bash
git clone https://github.com/liaoyl830/qwen-codex-responses-proxy-guide.git
cd qwen-codex-responses-proxy-guide
```

2. Copy the example files:

```bash
cp config.example.yaml config.yaml
cp .env.example .env
```

3. Edit `config.yaml`, keep the models you want to enable, and replace:

```yaml
YOUR_QWEN_API_KEY
YOUR_DEEPSEEK_API_KEY
YOUR_MINIMAX_API_KEY
YOUR_GLM_API_KEY
YOUR_MIMO_API_KEY
YOUR_PROXY_API_KEY
```

Generate a strong proxy key, for example:

```bash
openssl rand -hex 32
```

4. Start the proxy:

```bash
docker compose up -d
```

By default, the service is exposed on server port `3002`. The client Base URL is:

```text
http://YOUR_SERVER_IP:3002/v1
```

For production, HTTPS is strongly recommended:

```text
https://api.example.com/v1
```

## Windows / macOS Local Quick Start

Use the localhost-only Compose file:

```bash
docker compose -f docker-compose.local.yml up -d
```

Use this Base URL in Codex:

```text
http://127.0.0.1:3002/v1
```

Windows PowerShell:

```powershell
$env:CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

macOS / Linux shell:

```bash
export CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

## Test the Proxy

Choose a Base URL for your deployment mode first:

```text
Cloud server: http://YOUR_SERVER_IP:3002/v1 or https://api.example.com/v1
Local desktop: http://127.0.0.1:3002/v1
```

List models:

```bash
curl BASE_URL/models \
  -H "Authorization: Bearer YOUR_PROXY_API_KEY"
```

Test the Responses API:

```bash
curl BASE_URL/responses \
  -H "Authorization: Bearer YOUR_PROXY_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-5.2",
    "input": "Say hello in one short sentence."
  }'
```

To test a real upstream model name directly, set `model` to a name exposed in `config.yaml`, such as `deepseek-chat`, `MiniMax-M2.7`, `glm-4.7`, `mimo-v2-pro`, or `qwen3.6-plus`.

## Codex Configuration

Add a custom provider to your Codex `config.toml` and replace `base_url` for your deployment mode:

```text
Cloud server: http://YOUR_SERVER_IP:3002/v1 or https://api.example.com/v1
Local desktop: http://127.0.0.1:3002/v1
```

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

Set the proxy key before starting Codex:

```bash
export CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

Windows PowerShell:

```powershell
$env:CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

## Model Aliases

The example config exposes two kinds of model names:

- `gpt-5.2`: a client-facing Codex alias, mapped to Qwen by default.
- Real upstream names, such as `qwen3.6-plus`, `deepseek-chat`, `MiniMax-M2.7`, and `glm-4.7`.

To map `gpt-5.2` to DeepSeek, MiniMax, or GLM instead, edit the `name: gpt-5.2` entry in `config.yaml` and change its `backend`, `api_key`, and `model`.

## Security Notes

- Never commit real API keys to Git.
- For cloud deployments, do not expose the proxy publicly without authentication.
- Use HTTPS for cloud production deployments. HTTPS is usually unnecessary for localhost-only desktop deployments.
- Rotate upstream and proxy keys immediately if they are leaked.
- Provider billing, context caching, tool calling, and rate limits differ. Test with your real Codex workflow before production use.
- This is a compatibility-layer deployment guide, not an official OpenAI, Alibaba Cloud, DeepSeek, MiniMax, Zhipu, or MiMo product.

More docs:

- [Codex configuration](docs/codex-config.md)
- [Provider Catalog](docs/providers.md)
- [Parameter compatibility](docs/parameter-compatibility.md)
- [Windows / macOS local deployment](docs/local-desktop.md)
- [Source documentation](docs/sources.md)
- [Security](docs/security.md)
- [Troubleshooting](docs/troubleshooting.md)

## License

MIT
