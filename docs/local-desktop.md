# Windows / macOS Local Deployment

## 中文

本机部署适合只在当前电脑上使用 Codex，不需要云服务器，也不需要把代理暴露给手机或其他设备。默认访问地址是：

```text
http://127.0.0.1:3002/v1
```

本机版使用 `docker-compose.local.yml`，端口只绑定到 `127.0.0.1`。这比云服务器版更适合个人电脑，因为它不会主动暴露到局域网。

## Windows 版本

### 1. 安装 Docker Desktop

安装 Docker Desktop for Windows，并启用 WSL 2 backend。安装完成后打开 PowerShell，确认 Docker 可用：

```powershell
docker --version
docker compose version
```

### 2. 下载并进入项目

```powershell
git clone https://github.com/liaoyl830/qwen-codex-responses-proxy-guide.git
cd qwen-codex-responses-proxy-guide
```

如果没有 Git，也可以从 GitHub 下载 ZIP 后解压。

### 3. 复制并编辑配置

```powershell
Copy-Item config.example.yaml config.yaml
Copy-Item .env.example .env
notepad config.yaml
```

在 `config.yaml` 里替换：

```text
YOUR_QWEN_API_KEY
YOUR_DEEPSEEK_API_KEY
YOUR_MINIMAX_API_KEY
YOUR_GLM_API_KEY
YOUR_MIMO_API_KEY
YOUR_PROXY_API_KEY
```

只保留你实际要用的厂商和模型即可。

### 4. 启动本机代理

```powershell
docker compose -f docker-compose.local.yml up -d
```

查看运行状态：

```powershell
docker compose -f docker-compose.local.yml ps
```

测试模型列表：

```powershell
curl.exe http://127.0.0.1:3002/v1/models -H "Authorization: Bearer YOUR_PROXY_API_KEY"
```

### 5. 配置 Codex

`config.toml` 示例：

```toml
model_provider = "china_llm_proxy"
model = "gpt-5.2"
web_search = "disabled"

[model_providers.china_llm_proxy]
name = "China LLM Proxy"
wire_api = "responses"
base_url = "http://127.0.0.1:3002/v1"
env_key = "CHINA_LLM_PROXY_API_KEY"
```

PowerShell 临时设置：

```powershell
$env:CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

Windows 用户级永久设置：

```powershell
setx CHINA_LLM_PROXY_API_KEY "YOUR_PROXY_API_KEY"
```

使用 `setx` 后需要重启 Codex，必要时也重启终端。

## macOS 版本

### 1. 安装 Docker Desktop

按你的芯片类型安装 Docker Desktop for Mac：

- Apple Silicon：M1 / M2 / M3 / M4 等
- Intel：Intel Mac

安装完成后打开 Terminal，确认 Docker 可用：

```bash
docker --version
docker compose version
```

### 2. 下载并进入项目

```bash
git clone https://github.com/liaoyl830/qwen-codex-responses-proxy-guide.git
cd qwen-codex-responses-proxy-guide
```

如果没有 Git，也可以从 GitHub 下载 ZIP 后解压。

### 3. 复制并编辑配置

```bash
cp config.example.yaml config.yaml
cp .env.example .env
nano config.yaml
```

在 `config.yaml` 里替换：

```text
YOUR_QWEN_API_KEY
YOUR_DEEPSEEK_API_KEY
YOUR_MINIMAX_API_KEY
YOUR_GLM_API_KEY
YOUR_MIMO_API_KEY
YOUR_PROXY_API_KEY
```

只保留你实际要用的厂商和模型即可。

### 4. 启动本机代理

```bash
docker compose -f docker-compose.local.yml up -d
```

查看运行状态：

```bash
docker compose -f docker-compose.local.yml ps
```

测试模型列表：

```bash
curl http://127.0.0.1:3002/v1/models \
  -H "Authorization: Bearer YOUR_PROXY_API_KEY"
```

### 5. 配置 Codex

`config.toml` 示例：

```toml
model_provider = "china_llm_proxy"
model = "gpt-5.2"
web_search = "disabled"

[model_providers.china_llm_proxy]
name = "China LLM Proxy"
wire_api = "responses"
base_url = "http://127.0.0.1:3002/v1"
env_key = "CHINA_LLM_PROXY_API_KEY"
```

Terminal 临时设置：

```bash
export CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

如果 Codex 是从 Finder 或 Dock 启动，GUI 应用可能拿不到 shell 里的 `export`。可以使用：

```bash
launchctl setenv CHINA_LLM_PROXY_API_KEY "YOUR_PROXY_API_KEY"
```

设置后重启 Codex。

## 本机版和云服务器版的区别

| 项目 | 本机版 | 云服务器版 |
| --- | --- | --- |
| Base URL | `http://127.0.0.1:3002/v1` | `https://api.example.com/v1` 或服务器 IP |
| 可用范围 | 当前电脑 | 多设备/公网 |
| HTTPS | 通常不需要 | 强烈建议 |
| 安全边界 | 只绑定 localhost | 需要防火墙、HTTPS、强代理 Key |
| 适合场景 | 单人本机 Codex | 长期运行、多人或多设备 |

## 常用维护命令

停止：

```bash
docker compose -f docker-compose.local.yml down
```

重启：

```bash
docker compose -f docker-compose.local.yml restart
```

查看日志：

```bash
docker compose -f docker-compose.local.yml logs -f
```

更新镜像：

```bash
docker compose -f docker-compose.local.yml pull
docker compose -f docker-compose.local.yml up -d
```

## English

Local deployment is for using Codex on the same computer. It does not require a cloud server and does not expose the proxy to phones or other devices by default. The default Base URL is:

```text
http://127.0.0.1:3002/v1
```

The local mode uses `docker-compose.local.yml`, which binds the port only to `127.0.0.1`. This is better for personal computers because it avoids accidental LAN exposure.

## Windows

### 1. Install Docker Desktop

Install Docker Desktop for Windows and enable the WSL 2 backend. Then open PowerShell and verify:

```powershell
docker --version
docker compose version
```

### 2. Download and enter the project

```powershell
git clone https://github.com/liaoyl830/qwen-codex-responses-proxy-guide.git
cd qwen-codex-responses-proxy-guide
```

If Git is not installed, download the ZIP from GitHub and extract it.

### 3. Copy and edit config

```powershell
Copy-Item config.example.yaml config.yaml
Copy-Item .env.example .env
notepad config.yaml
```

Replace the placeholders you need:

```text
YOUR_QWEN_API_KEY
YOUR_DEEPSEEK_API_KEY
YOUR_MINIMAX_API_KEY
YOUR_GLM_API_KEY
YOUR_MIMO_API_KEY
YOUR_PROXY_API_KEY
```

Keep only the providers and models you actually use.

### 4. Start the local proxy

```powershell
docker compose -f docker-compose.local.yml up -d
```

Check status:

```powershell
docker compose -f docker-compose.local.yml ps
```

Test model listing:

```powershell
curl.exe http://127.0.0.1:3002/v1/models -H "Authorization: Bearer YOUR_PROXY_API_KEY"
```

### 5. Configure Codex

`config.toml` example:

```toml
model_provider = "china_llm_proxy"
model = "gpt-5.2"
web_search = "disabled"

[model_providers.china_llm_proxy]
name = "China LLM Proxy"
wire_api = "responses"
base_url = "http://127.0.0.1:3002/v1"
env_key = "CHINA_LLM_PROXY_API_KEY"
```

Temporary PowerShell variable:

```powershell
$env:CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

Persistent user-level variable:

```powershell
setx CHINA_LLM_PROXY_API_KEY "YOUR_PROXY_API_KEY"
```

Restart Codex after using `setx`; restart your terminal if needed.

## macOS

### 1. Install Docker Desktop

Install Docker Desktop for Mac for your chip:

- Apple Silicon: M1 / M2 / M3 / M4 and newer
- Intel: Intel Mac

Then verify in Terminal:

```bash
docker --version
docker compose version
```

### 2. Download and enter the project

```bash
git clone https://github.com/liaoyl830/qwen-codex-responses-proxy-guide.git
cd qwen-codex-responses-proxy-guide
```

If Git is not installed, download the ZIP from GitHub and extract it.

### 3. Copy and edit config

```bash
cp config.example.yaml config.yaml
cp .env.example .env
nano config.yaml
```

Replace the placeholders you need:

```text
YOUR_QWEN_API_KEY
YOUR_DEEPSEEK_API_KEY
YOUR_MINIMAX_API_KEY
YOUR_GLM_API_KEY
YOUR_MIMO_API_KEY
YOUR_PROXY_API_KEY
```

Keep only the providers and models you actually use.

### 4. Start the local proxy

```bash
docker compose -f docker-compose.local.yml up -d
```

Check status:

```bash
docker compose -f docker-compose.local.yml ps
```

Test model listing:

```bash
curl http://127.0.0.1:3002/v1/models \
  -H "Authorization: Bearer YOUR_PROXY_API_KEY"
```

### 5. Configure Codex

`config.toml` example:

```toml
model_provider = "china_llm_proxy"
model = "gpt-5.2"
web_search = "disabled"

[model_providers.china_llm_proxy]
name = "China LLM Proxy"
wire_api = "responses"
base_url = "http://127.0.0.1:3002/v1"
env_key = "CHINA_LLM_PROXY_API_KEY"
```

Temporary shell variable:

```bash
export CHINA_LLM_PROXY_API_KEY="YOUR_PROXY_API_KEY"
```

If Codex is started from Finder or Dock, GUI apps may not inherit shell `export` values. Use:

```bash
launchctl setenv CHINA_LLM_PROXY_API_KEY "YOUR_PROXY_API_KEY"
```

Restart Codex after setting it.

## Local vs Cloud

| Item | Local desktop | Cloud server |
| --- | --- | --- |
| Base URL | `http://127.0.0.1:3002/v1` | `https://api.example.com/v1` or server IP |
| Scope | Current computer | Multiple devices / internet |
| HTTPS | Usually unnecessary | Strongly recommended |
| Security boundary | localhost only | Firewall, HTTPS, and strong proxy key |
| Best for | Personal Codex usage | Always-on shared service |

## Common Maintenance Commands

Stop:

```bash
docker compose -f docker-compose.local.yml down
```

Restart:

```bash
docker compose -f docker-compose.local.yml restart
```

View logs:

```bash
docker compose -f docker-compose.local.yml logs -f
```

Update image:

```bash
docker compose -f docker-compose.local.yml pull
docker compose -f docker-compose.local.yml up -d
```
