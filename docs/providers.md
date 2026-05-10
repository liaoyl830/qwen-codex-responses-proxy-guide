# Provider Catalog

## 中文

这份表格只记录已公开文档确认或服务商文档明确给出的 OpenAI 兼容入口。实际模型名、上下文长度、价格和限流会变化，生产部署前应以各平台控制台为准。

| 平台 | Base URL | Chat Completions 路径 | 示例模型 | 上下文窗口 | 备注 |
| --- | --- | --- | --- | --- | --- |
| Qwen / 阿里百炼标准接口 | `https://dashscope.aliyuncs.com/compatible-mode/v1` | `/chat/completions` | `qwen-plus` | 1M | 北京地域；新加坡/美国地域有不同域名 |
| Qwen / 阿里百炼 Coding Plan | `https://coding.dashscope.aliyuncs.com/v1` | `/chat/completions` | `qwen3.6-plus` | 1M | Coding 套餐专属入口 |
| DeepSeek | `https://api.deepseek.com/v1` | `/chat/completions` | `deepseek-chat`, `deepseek-reasoner` | 64K | `deepseek-reasoner` 不支持 Function Calling，不建议作为默认 Codex agent 模型 |
| MiniMax | `https://api.minimax.io/v1` | `/chat/completions` | `MiniMax-M2.7`, `MiniMax-M2.5` | 204,800 | 官方文档同时提供 Anthropic 兼容入口；本方案使用 OpenAI 兼容入口 |
| GLM / 智谱 | `https://open.bigmodel.cn/api/paas/v4` | `/chat/completions` | `glm-4.7`, `glm-4.6`, `glm-4.5` | 200K | Coding 套餐可能有专属端点，注意区分 |
| MiMo | `https://api.mimo-v2.com/v1` 或你的 MiMo 服务商地址 | `/chat/completions` | `mimo-v2-pro`, `mimo-v2.5-pro` | 1M | MiMo 生态里存在多个服务商/域名，部署前以你的控制台为准 |

## 选择默认模型

Codex 只需要看到一个可用模型名。你可以保留 `gpt-5.2` 作为稳定别名，然后把它映射到任何一个上游模型。

默认映射到 Qwen：

```yaml
- name: gpt-5.2
  backend: https://coding.dashscope.aliyuncs.com/v1
  api_key: YOUR_QWEN_API_KEY
  model: qwen3.6-plus
```

改成 DeepSeek：

```yaml
- name: gpt-5.2
  backend: https://api.deepseek.com/v1
  api_key: YOUR_DEEPSEEK_API_KEY
  model: deepseek-chat
```

改成 MiniMax：

```yaml
- name: gpt-5.2
  backend: https://api.minimax.io/v1
  api_key: YOUR_MINIMAX_API_KEY
  model: MiniMax-M2.7
```

改成 GLM：

```yaml
- name: gpt-5.2
  backend: https://open.bigmodel.cn/api/paas/v4
  api_key: YOUR_GLM_API_KEY
  model: glm-4.7
```

改成 MiMo：

```yaml
- name: gpt-5.2
  backend: https://api.mimo-v2.com/v1
  api_key: YOUR_MIMO_API_KEY
  model: mimo-v2-pro
```

## 注意事项

- 上游如果原生支持 `/v1/responses`，可以考虑直接接入 Codex，不一定需要这个代理。
- 上游如果只支持 `/v1/chat/completions`，本方案用代理把 Codex 的 Responses 请求转换为 Chat Completions。
- 不同厂商对 `developer` role、工具调用、流式输出、reasoning 内容的兼容程度不同，必须用真实 Codex 工作流测试。
- 不要用供应商的营销模型名替代 API 模型名，API 里要写控制台/文档给出的准确模型 ID。
- 通用配置不设置 `defaults`，避免把 `max_tokens`、`reasoning_effort` 等非统一参数注入到不兼容的上游。

## English

This table only lists OpenAI-compatible endpoints confirmed by public documentation or provider-facing docs. Model names, context windows, pricing, and rate limits can change. Always verify production settings in the provider console.

| Provider | Base URL | Chat Completions path | Example models | Context window | Notes |
| --- | --- | --- | --- | --- | --- |
| Qwen / Alibaba Model Studio standard endpoint | `https://dashscope.aliyuncs.com/compatible-mode/v1` | `/chat/completions` | `qwen-plus` | 1M | Beijing region; Singapore and US regions use different domains |
| Qwen / Alibaba Coding Plan | `https://coding.dashscope.aliyuncs.com/v1` | `/chat/completions` | `qwen3.6-plus` | 1M | Coding Plan dedicated endpoint |
| DeepSeek | `https://api.deepseek.com/v1` | `/chat/completions` | `deepseek-chat`, `deepseek-reasoner` | 64K | `deepseek-reasoner` does not support Function Calling and is not recommended as the default Codex agent model |
| MiniMax | `https://api.minimax.io/v1` | `/chat/completions` | `MiniMax-M2.7`, `MiniMax-M2.5` | 204,800 | MiniMax also offers an Anthropic-compatible endpoint; this guide uses OpenAI compatibility |
| GLM / Zhipu | `https://open.bigmodel.cn/api/paas/v4` | `/chat/completions` | `glm-4.7`, `glm-4.6`, `glm-4.5` | 200K | Coding plans may have dedicated endpoints |
| MiMo | `https://api.mimo-v2.com/v1` or your MiMo provider endpoint | `/chat/completions` | `mimo-v2-pro`, `mimo-v2.5-pro` | 1M | The MiMo ecosystem has multiple service domains; verify your console endpoint |

## Choosing the Default Model

Codex only needs one stable model name. You can keep `gpt-5.2` as a stable alias and map it to any upstream model.

Map to Qwen:

```yaml
- name: gpt-5.2
  backend: https://coding.dashscope.aliyuncs.com/v1
  api_key: YOUR_QWEN_API_KEY
  model: qwen3.6-plus
```

Map to DeepSeek:

```yaml
- name: gpt-5.2
  backend: https://api.deepseek.com/v1
  api_key: YOUR_DEEPSEEK_API_KEY
  model: deepseek-chat
```

Map to MiniMax:

```yaml
- name: gpt-5.2
  backend: https://api.minimax.io/v1
  api_key: YOUR_MINIMAX_API_KEY
  model: MiniMax-M2.7
```

Map to GLM:

```yaml
- name: gpt-5.2
  backend: https://open.bigmodel.cn/api/paas/v4
  api_key: YOUR_GLM_API_KEY
  model: glm-4.7
```

Map to MiMo:

```yaml
- name: gpt-5.2
  backend: https://api.mimo-v2.com/v1
  api_key: YOUR_MIMO_API_KEY
  model: mimo-v2-pro
```

## Notes

- If an upstream provider natively supports `/v1/responses`, you may connect Codex directly and skip this proxy.
- If an upstream provider only supports `/v1/chat/completions`, this proxy translates Codex Responses requests into Chat Completions requests.
- Provider support for `developer` roles, tool calls, streaming, and reasoning content varies. Test with your real Codex workflow.
- Use exact API model IDs from the provider console or docs, not marketing names.
- The generic config does not set `defaults`, avoiding injection of non-universal parameters such as `max_tokens` and `reasoning_effort`.
