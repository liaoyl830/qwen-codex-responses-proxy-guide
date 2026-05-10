# Troubleshooting

## 中文

### Codex 报 403 not authorized for requested model

通常是 Codex 请求的模型名没有写进 `keys[].models`。例如 Codex 请求 `gpt-5.2`，那么配置里必须允许：

```yaml
keys:
  - key: YOUR_PROXY_API_KEY
    name: codex-user
    models: [gpt-5.2, qwen3.6-plus]
```

### 分组下模型无可用渠道

这是上游中转系统常见错误。检查模型名、分组、渠道状态和 Key 是否匹配。这个仓库的直连方案不依赖 New API 分组。

### upstream error: do request failed

常见原因：

- 上游 Base URL 写错。
- 上游 Key 无效或已过期。
- 上游套餐不支持该模型。
- 服务器无法访问上游网络。

### 429 Too Many Requests

可能是上游限流、代理 Key 重试过多、或客户端并发过高。先降低并发并确认上游账户额度。

### Codex 模型列表里没有真实模型名

可以用别名。把 Codex 的 `model` 设置为代理暴露的名字，例如 `gpt-5.2`。代理再把它转发成真实模型，例如 `qwen3.6-plus`、`deepseek-chat`、`MiniMax-M2.7` 或 `glm-4.7`。

### 某个厂商能 curl 成功，但 Codex 不稳定

不同厂商对流式输出、工具调用、reasoning 字段、上下文长度和超时的支持不完全一致。先用短输入测试，再逐步测试代码修改、长上下文、工具调用和多轮对话。

### /v1/models 正常，但 Codex 不能用

检查：

- `base_url` 是否以 `/v1` 结尾。
- `wire_api` 是否是 `responses`。
- 环境变量里的 Key 是否是代理 Key，不是上游 Key。
- `config.yaml` 里的 `responses_mode` 是否是 `translate`。

## English

### Codex returns 403 not authorized for requested model

The model name requested by Codex is probably not allowed in `keys[].models`. For example, if Codex requests `gpt-5.2`, the key must allow it:

```yaml
keys:
  - key: YOUR_PROXY_API_KEY
    name: codex-user
    models: [gpt-5.2, qwen3.6-plus]
```

### No available distributor/channel for the model

This is common in upstream aggregation systems. Check the model name, group, channel status, and key. This guide uses a direct proxy and does not depend on New API groups.

### upstream error: do request failed

Common causes:

- Wrong upstream Base URL.
- Invalid or expired upstream key.
- The upstream plan does not include the requested model.
- The server cannot reach the upstream network.

### 429 Too Many Requests

This may be upstream rate limiting, repeated authentication retries, or excessive client concurrency. Lower concurrency first and check upstream quota.

### Codex model picker does not show the real upstream model name

Use an alias. Set Codex `model` to a proxy-exposed name such as `gpt-5.2`. The proxy rewrites it to a real upstream model such as `qwen3.6-plus`, `deepseek-chat`, `MiniMax-M2.7`, or `glm-4.7`.

### A provider works with curl but is unstable in Codex

Provider compatibility differs for streaming, tool calls, reasoning fields, context windows, and timeouts. Start with short prompts, then test code edits, long context, tool use, and multi-turn conversations.

### /v1/models works, but Codex fails

Check:

- `base_url` ends with `/v1`.
- `wire_api` is `responses`.
- The environment variable contains the proxy key, not the upstream key.
- `responses_mode` is `translate` in `config.yaml`.
