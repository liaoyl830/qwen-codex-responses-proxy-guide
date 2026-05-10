# Security

## 中文

这类代理会同时接触客户端代理 Key 和上游模型 Key，所以要按生产服务处理。

建议：

- 不要把真实 Key、服务器 IP、域名、密码提交到 Git。
- `config.yaml` 和 `.env` 必须留在 `.gitignore` 里。
- 对公网开放时必须启用 `keys` 鉴权。
- 生产环境建议使用 HTTPS。
- 尽量只开放必要端口，例如 `80`、`443` 或代理端口。
- 如果使用反向代理，可以把容器只绑定到本机，再由 Nginx / Caddy 暴露 HTTPS。
- 泄露后立即轮换上游 API Key 和代理 Key。
- 不要把这个代理当成无限制公共服务使用，避免账单风险。
- 多厂商聚合时，建议为每个厂商设置独立 Key，便于单独限流、停用和排查账单。
- 对外提供服务时，不要把真实上游模型、真实供应商 Key 或内部路由策略暴露给最终用户。

## English

This proxy handles both client-facing proxy keys and upstream model keys, so treat it as a production service.

Recommendations:

- Never commit real keys, server IPs, domains, or passwords to Git.
- Keep `config.yaml` and `.env` in `.gitignore`.
- Keep `keys` authentication enabled when exposing the service publicly.
- Use HTTPS in production.
- Expose only required ports, such as `80`, `443`, or the proxy port.
- If using a reverse proxy, bind the container locally and expose HTTPS through Nginx / Caddy.
- Rotate both upstream and proxy keys immediately after a leak.
- Do not run the proxy as an unrestricted public service unless you have billing controls.
- When aggregating multiple providers, use separate keys per provider so you can limit, disable, and audit them independently.
- If you expose this service to end users, do not expose real upstream keys, internal routing rules, or private provider credentials.
