# ccLoad

## 一句话说清楚

ccLoad 是一个 LLM API 代理 / 负载均衡器。把多个上游 API Key 统一管理，对外暴露一个不变的 base URL，自动做多渠道轮询、故障转移、日志追踪。

仓库：[https://github.com/caidaoli/ccLoad](https://github.com/caidaoli/ccLoad)

## 环境要求

- Docker
- Docker Compose v2+

```bash
docker --version
docker compose version
```

## Docker 部署

### 1. 创建部署目录并准备文件

创建 `docker-compose.yml`，这个是经过验证的最小配置：

```yaml
version: '3.8'

services:
  ccload:
    image: ghcr.io/caidaoli/ccload:latest
    container_name: ccload
    user: root
    restart: unless-stopped
    ports:
      - "18080:8080"
    environment:
      - PORT=8080
      - SQLITE_PATH=/app/data/ccload.db
      - GIN_MODE=release
      - CCLOAD_PASS=请替换为你的管理密码
      - TZ=Asia/Shanghai
      # 可选：启动时预置 API 访问令牌
      # - CCLOAD_API_TOKENS=token1|production,token2|development
    volumes:
      - ./data:/app/data
    healthcheck:
      test: ["CMD", "wget", "--no-verbose", "--tries=1", "--spider", "http://localhost:18080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### 2. 启动

```bash
mkdir -p data                                     # 创建数据目录
docker compose up -d                              # Start services —— 启动
docker compose ps                                 # Check status —— 确认 Up
docker compose logs --tail 50 ccload              # 看最近 50 行日志
```

### 3. 验证

```bash
# Linux/macOS
curl -i http://localhost:18080/health

# Windows PowerShell
Invoke-WebRequest -UseBasicParsing http://localhost:18080/health
```

返回 `200` 即正常。

### 4. 访问管理界面

```text
http://localhost:18080
```

用 `CCLOAD_PASS` 设置的密码登录。

## 配置上游渠道

登录管理界面后：

1. 进入渠道管理页面
2. 添加渠道，填入你的上游 API 地址和 API Key
3. 可以添加多个渠道实现负载均衡和故障转移

你还可以在 Web 管理界面配置：

- API 访问令牌管理：`http://localhost:18080/web/tokens.html`
- 系统设置：`http://localhost:18080/web/settings.html`（日志保留天数、最大重试次数、超时时间等）

## 生成 API Key 并接入 cc-switch

### 第一步：在 ccLoad 创建 token

打开 `http://localhost:18080/web/tokens.html`，创建一个 API 访问令牌，记下这个 token。

### 第二步：在 cc-switch 创建 provider

Claude Code / Codex / Gemini 都指向 ccLoad 的地址：

| CLI | base URL | 说明 |
|-----|----------|------|
| Claude Code | `http://localhost:18080` |  |
| Codex | `http://localhost:18080/v1` | OpenAI 兼容接口 |
| Gemini | `http://localhost:18080` |                 |

具体操作：

1. 在 cc-switch 中新建自定义 provider

2. base URL 填 `http://localhost:18080

   ![image-20260512105744162](https://wanwurong.oss-cn-beijing.aliyuncs.com/pic/image-20260512105744162.png)

   ![image-20260512110542310](https://wanwurong.oss-cn-beijing.aliyuncs.com/pic/image-20260512110542310.png)

3. API Key 填 ccLoad 创建的 token

4. 保存，切换到这个 provider

5. 重启终端，打开对应 CLI，发送请求验证

### 第三步：验证链路

```bash
claude
# 或
codex
```

问一句：

```text
what model are you using right now?
```

然后在 ccLoad 管理界面看请求日志，确认请求经过了代理。

## 工作原理

```text
Claude Code / Codex / Gemini CLI
        ↓
cc-switch（切换 provider 配置）
        ↓
ccLoad（代理层，多渠道负载均衡 + 日志追踪）
        ↓
上游 API 1 / API 2 / API 3（自动轮询、故障转移）
```

## 常用运维命令

```bash
docker compose ps                           # 查看容器状态
docker compose logs -f ccload               # 实时日志
docker compose restart ccload               # 重启
docker compose down                         # 停止
docker compose pull                         # 拉新镜像
docker compose up -d                        # 升级后重新启动
```

## 数据备份

```bash
docker compose down
cp ./data/ccload.db ./ccload.db.bak             # Linux/macOS
# 或 Copy-Item .\data\ccload.db .\ccload.db.bak  # Windows PowerShell
docker compose up -d
```

## 排错

### 端口被占用

```yaml
ports:
  - "18081:8080"
```

访问改为 `http://localhost:18081`。

### 启动后无法访问

```bash
docker compose ps                           # 容器是否 Up
docker compose logs --tail 100 ccload       # 日志有无报错
curl -i http://localhost:18080/health       # 健康检查是否 200
```

重点确认 `CCLOAD_PASS` 是否已设置（未设置会拒绝启动）。

### Docker 权限

Windows：确认 Docker Desktop 已启动。必要时管理员权限开 PowerShell。

Linux：`sudo docker compose ps`，或把用户加入 `docker` 组。

---