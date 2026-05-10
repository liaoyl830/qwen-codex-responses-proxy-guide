# Parameter Compatibility

## 中文

`config.example.yaml` 故意不再为各模型写 `defaults`。原因是国内 OpenAI 兼容接口并没有完全统一采样参数和思考参数。

go-llm-proxy 的 `defaults` 会在客户端没有传参时注入默认值。这个能力适合自托管 vLLM、llama-server 或明确兼容 OpenAI 参数的后端；但对于多厂商通用配置，过早写入默认参数反而容易造成兼容问题。

## 已检查的问题

| 项目 | 结论 | 处理方式 |
| --- | --- | --- |
| `context_window` | 必须按模型规格写，否则 Codex 的上下文估算会不准 | 已在 `config.example.yaml` 中显式设置 |
| `max_new_tokens` | go-llm-proxy 文档说明会映射为 `max_tokens`；MiniMax/MiMo 文档使用 `max_completion_tokens`，不同厂商不完全一致 | 通用配置不设置，交给 Codex 请求或上游默认值 |
| `reasoning_effort` / `model_reasoning_effort` | OpenAI Responses 里是标准概念，但 GLM/MiMo/Qwen/DeepSeek 的思考模式参数并不统一 | 通用配置和 Codex 示例都不默认设置，避免给上游传错参数 |
| `temperature` / `top_p` / penalty | DeepSeek reasoner 文档明确说明这些参数无效或不支持；其他厂商范围也不同 | 通用配置不设置 |
| Function Calling | DeepSeek reasoner 文档明确不支持 Function Calling；Codex 工具调用依赖函数调用能力 | 标注为规划/聊天可用，不建议作为默认 Codex agent 模型 |
| Base URL | 各厂商路径差异较大 | 已按 provider 文档分别写入 |
| 鉴权 Header | 大多数 OpenAI 兼容接口支持 Bearer；MiMo 文档同时展示 OpenAI SDK 和 `api-key` header 示例 | 保留 OpenAI 兼容方式，部署前用 `/v1/models` 和 `/v1/chat/completions` 验证 |

## 推荐原则

- 通用模板里只写跨厂商稳定字段：`name`、`backend`、`api_key`、`model`、`responses_mode`、`context_window`、`timeout`。
- 输出长度、温度、思考开关等参数不要写进通用模板；只在确认单一上游支持后再加。
- 如果你只使用单一厂商，可以按该厂商文档自行增加 `defaults`，但要先确认上游实际接受的字段。
- 对 Codex 这类 agent 工具，默认模型应优先选择支持工具调用的模型。

## English

`config.example.yaml` intentionally does not define per-model `defaults`. China-hosted OpenAI-compatible providers do not fully agree on sampling and reasoning parameters.

go-llm-proxy injects `defaults` only when the client does not send its own parameters. This is useful for self-hosted vLLM, llama-server, or backends that clearly accept OpenAI-style sampling fields. For a multi-provider template, injecting defaults too early can break compatibility.

## Checked Issues

| Item | Conclusion | Handling |
| --- | --- | --- |
| `context_window` | Must match model specs, or Codex context estimation becomes inaccurate | Explicitly set in `config.example.yaml` |
| `max_new_tokens` | go-llm-proxy maps it to `max_tokens`; MiniMax/MiMo docs use `max_completion_tokens`; providers differ | Not set in the generic config; let Codex requests or provider defaults decide |
| `reasoning_effort` / `model_reasoning_effort` | Standard in OpenAI Responses, but GLM/MiMo/Qwen/DeepSeek use different thinking controls | Not set in the generic config or Codex examples |
| `temperature` / `top_p` / penalties | DeepSeek reasoner documents these as ignored or unsupported; ranges vary by provider | Not set in the generic config |
| Function Calling | DeepSeek reasoner documents Function Calling as unsupported; Codex tool use depends on function calling | Marked as planning/chat capable, not recommended as the default Codex agent model |
| Base URL | Provider paths differ | Set per provider from docs |
| Auth header | Most OpenAI-compatible endpoints support Bearer; MiMo docs show both OpenAI SDK and `api-key` examples | Keep OpenAI-compatible auth and verify with `/v1/models` and `/v1/chat/completions` before production |

## Recommended Rule

- Keep the generic template to stable cross-provider fields: `name`, `backend`, `api_key`, `model`, `responses_mode`, `context_window`, and `timeout`.
- Do not put output length, temperature, or reasoning controls in the generic template; add them only after verifying a single upstream provider supports them.
- If you use only one provider, add `defaults` only after checking that provider's accepted parameters.
- For Codex-style agents, prefer default models that support tool calling.
