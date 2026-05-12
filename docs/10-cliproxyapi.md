# CLIProxyAPI

## 一句话说清楚

CLIProxyAPI 把 Gemini CLI、Claude Code、Codex 包装成兼容 OpenAI / Gemini / Claude / Codex 的 API 服务。简单说就是：**用本地 CLI 的免费额度，通过 API 给其他工具用**。

仓库：[https://github.com/router-for-me/CLIProxyAPI](https://github.com/router-for-me/CLIProxyAPI)

官方帮助站：[https://help.router-for.me/](https://help.router-for.me/)

## 环境要求

- Docker
- Docker Compose v2+

```bash
docker --version
docker compose version
```

## Docker 部署

### 1. 克隆项目并准备配置

```bash
# Clone project —— 克隆项目
git clone https://github.com/router-for-me/CLIProxyAPI.git
cd CLIProxyAPI

# Prepare config —— 从示例复制一份配置文件
cp config.example.yaml config.yaml

# Windows CMD 或 PowerShell 用：
# copy config.example.yaml config.yaml
```

然后编辑 `config.yaml`，按自己的需求调整。配置文件说明见：[https://help.router-for.me/configuration/basic.html](https://help.router-for.me/configuration/basic.html)

### 2. 启动

```bash
# Start services —— 启动（用预构建镜像，推荐）
docker compose up -d

# Check status —— 确认容器 Up
docker compose ps
```

### 3. 登录上游供应商

容器跑起来后，在容器内执行登录命令，获取对应 CLI 的认证：

```bash
# Login Gemini —— 登录 Gemini CLI
docker compose exec cli-proxy-api /CLIProxyAPI/CLIProxyAPI -no-browser --login

# Login Codex —— 登录 OpenAI Codex
docker compose exec cli-proxy-api /CLIProxyAPI/CLIProxyAPI -no-browser --codex-login

# Login Claude —— 登录 Claude Code
docker compose exec cli-proxy-api /CLIProxyAPI/CLIProxyAPI -no-browser --claude-login
```

每条命令执行后会打印一个 URL，在浏览器里打开完成 OAuth 登录。

### 4. 验证

```bash
# Linux/macOS
curl -i http://localhost:8317/health

# Windows PowerShell
Invoke-WebRequest -UseBasicParsing http://localhost:8317/health
```

### 5. 查看日志和停止

```bash
docker compose logs -f       # View logs —— 实时日志
docker compose down          # Stop —— 停止
```

## 接入 cc-switch

CLIProxyAPI 默认端口是 `8317`，支持 OpenAI / Gemini / Claude / Codex 兼容接口。

### 在 cc-switch 创建 provider

| CLI | base URL | 说明 |
|-----|----------|------|
| Claude Code | `http://localhost:8317/v1` | Claude 兼容接口 |
| Codex | `http://localhost:8317/v1` | OpenAI 兼容接口 |
| Gemini | `http://localhost:8317/v1` | Gemini 兼容接口 |

具体操作：

1. 在 cc-switch 新建自定义 provider
2. base URL 填 `http://localhost:8317/v1`
3. API Key 可以随便填一个值（CLIProxyAPI 不校验 Key，请求直接用容器内已登录的 CLI 身份）
4. 保存，切换到该 provider
5. 重启终端，打开对应 CLI，发送请求验证

### 验证链路

```bash
claude
# 或
codex
```

问一句确认请求经过了代理。

## 工作原理

```text
Claude Code / Codex / Gemini CLI（客户端）
        ↓
cc-switch（provider 切换）
        ↓
CLIProxyAPI（容器内运行的代理，持有 CLI 登录态）
        ↓
Gemini / OpenAI / Anthropic 官方服务（通过 CLI 的免费额度）
```

## 常用运维命令

```bash
docker compose ps                                    # 查看容器状态
docker compose logs -f                               # 实时日志
docker compose restart cli-proxy-api                 # 重启
docker compose down                                  # 停止
docker compose pull                                  # 拉新镜像
docker compose up -d                                 # 重新启动
```

## 与本指南其他工具的配合

| 工具 | 怎么配合 |
|------|---------|
| AxonHub | AxonHub 管模型供应商路由，CLIProxyAPI 提供免费 CLI 额度——可以把 CLIProxyAPI 作为 AxonHub 的上游渠道 |
| ccLoad | ccLoad 管多渠道负载均衡，可以把 CLIProxyAPI 作为一个渠道接入 |
| Trellis | 管项目流程和任务 |
| cc-switch | 切 provider 时切到 CLIProxyAPI |

## 常见坑

- 忘了先执行登录命令（`--login` / `--codex-login` / `--claude-login`）
- 配置文件没从 `config.example.yaml` 复制过来就直接启动
- CLI 的 OAuth 登录过期后需要重新登录
- 官方帮助站在 [https://help.router-for.me/](https://help.router-for.me/)，遇到配置问题优先看那边

---