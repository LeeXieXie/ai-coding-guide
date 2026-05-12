# AxonHub

## 一句话说清楚

AxonHub 是一个 AI 网关。部署后你用任何 SDK 都能调用任意厂商的模型，**只改 `base_url`，不改代码**。

仓库：[https://github.com/looplj/axonhub](https://github.com/looplj/axonhub)

## 环境要求

- Docker
- Docker Compose v2+

```bash
docker --version
docker compose version
```

## 方案一：SQLite 快速部署（个人测试、小团队）

### 1. 准备文件

创建部署目录，放入以下文件：

**`docker-compose.yml`**：

```yaml
services:
  axonhub:
    image: looplj/axonhub:latest
    container_name: axonhub
    restart: unless-stopped
    ports:
      - "18090:8090"
    working_dir: /data
    volumes:
      - ./docker/config.yml:/root/.config/axonhub/config.yml:ro
      - ./docker/data:/data
```

**`docker/config.yml`**：

```yaml
server:
  host: "0.0.0.0"
  port: 8090
  name: "AxonHub"
  request_timeout: "120s"
  llm_request_timeout: "10m"
  debug: false

db:
  dialect: "sqlite3"
  dsn: "file:/data/axonhub.db?cache=shared&_fk=1&_pragma=journal_mode(WAL)"

cache:
  mode: "memory"

log:
  level: "info"
  encoding: "json"
  output: "stdio"
```

创建空数据目录：

```bash
mkdir -p docker/data
```

### 2. 启动

```bash
docker compose up -d               # Start services —— 启动
docker compose ps                   # Check status —— 确认 Up
docker compose logs --tail 100 axonhub  # 看最近 100 行日志
```

### 3. 健康检查

```bash
# Linux/macOS
curl -i http://localhost:18090/health

# Windows PowerShell
Invoke-WebRequest -UseBasicParsing http://localhost:18090/health
```

返回 `200` 即正常。

### 4. 访问

```text
http://localhost:18090
```

部署在服务器上则替换为服务器 IP：

```text
http://服务器IP:18090
```

![image-20260512105132396](https://wanwurong.oss-cn-beijing.aliyuncs.com/pic/image-20260512105132396.png)

在点击渠道，选择添加渠道，填入第三方中转站的baseurl和apikey即可![image-20260512104844153](https://wanwurong.oss-cn-beijing.aliyuncs.com/pic/image-20260512104844153.png)

然后再api密钥这里创建apikey![image-20260512104942737](https://wanwurong.oss-cn-beijing.aliyuncs.com/pic/image-20260512104942737.png)

要填的内容可以点击眼睛查看

![image-20260512105042124](https://wanwurong.oss-cn-beijing.aliyuncs.com/pic/image-20260512105042124.png)

## 方案二：PostgreSQL 生产部署（多人、长期运行）

创建 `.env`：

```bash
DB_PASSWORD=请替换为强密码
```

创建 `docker-compose.yml`：

```yaml
services:
  postgres:
    image: postgres:16-alpine
    container_name: axonhub-postgres
    environment:
      POSTGRES_DB: axonhub
      POSTGRES_USER: axonhub
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - axonhub-network
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U axonhub"]
      interval: 10s
      timeout: 5s
      retries: 5

  axonhub:
    image: looplj/axonhub:latest
    container_name: axonhub-app
    environment:
      AXONHUB_SERVER_HOST: 0.0.0.0
      AXONHUB_SERVER_PORT: 8090
      AXONHUB_DB_DIALECT: postgres
      AXONHUB_DB_DSN: postgres://axonhub:${DB_PASSWORD}@postgres:5432/axonhub?sslmode=disable
      AXONHUB_CACHE_MODE: memory
      AXONHUB_LOG_LEVEL: info
      AXONHUB_LOG_ENCODING: json
      AXONHUB_LOG_OUTPUT: stdio
    ports:
      - "18090:8090"
    networks:
      - axonhub-network
    depends_on:
      postgres:
        condition: service_healthy
    restart: unless-stopped
    healthcheck:
      test:
        [
          "CMD",
          "wget",
          "--no-verbose",
          "--tries=1",
          "--spider",
          "http://localhost:8090/health"
        ]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

volumes:
  postgres_data:

networks:
  axonhub-network:
    driver: bridge
```

启动、检查、访问同上。

## 常用运维命令

```bash
docker compose ps                     # 查看容器状态
docker compose logs -f axonhub        # 实时日志
docker compose restart axonhub        # 重启
docker compose down                   # 停止
docker compose pull                   # 拉新镜像
docker compose up -d                  # 升级后重新启动
docker compose exec -T axonhub /app/axonhub version  # 查看版本
```

## 数据备份

### SQLite

```bash
docker compose down
cp ./docker/data/axonhub.db ./axonhub.db.bak    # Linux/macOS
# 或 Copy-Item .\docker\data\axonhub.db .\axonhub.db.bak  # Windows PowerShell
docker compose up -d
```

### PostgreSQL

```bash
docker compose exec -T postgres pg_dump -U axonhub axonhub > axonhub.sql   # 备份
docker compose exec -T postgres psql -U axonhub axonhub < axonhub.sql      # 恢复
```

## 排错

### 端口被占用

报 `port is already allocated` 时，改 `docker-compose.yml` 里的宿主机端口：

```yaml
ports:
  - "18091:8090"
```

然后 `docker compose up -d`，访问地址改为 `http://服务器IP:18091`。

### 启动后无法访问

按顺序查：

```bash
docker compose ps                        # 容器是否 Up
docker compose logs --tail 100 axonhub   # 日志是否显示 run server
curl -i http://localhost:18090/health    # 健康检查是否 200
```

重点确认：

- `server.host` 必须是 `0.0.0.0`
- 宿主机端口是否正确映射到容器 `8090`
- 防火墙 / 安全组是否放行

### Docker 权限

Windows：确认 Docker Desktop 已启动。必要时用管理员权限开 PowerShell。

Linux：如无权限，`sudo docker compose ps`，或把用户加入 `docker` 组后重新登录。

## 部署后怎么用

1. 打开 `http://localhost:18090`，按初始化向导创建管理员账号
2. 在通道管理中添加供应商（OpenAI / Gemini / DeepSeek 等），填入 API Key
3. 创建 API Key，把代码里的 `base_url` 指向 AxonHub：

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:18090/v1",   # 指向 AxonHub
    api_key="你的axonhub-key"               # 在管理界面创建的 Key
)
```

4. 想切模型？只改 `model` 参数，SDK 代码不变。

## 公网部署建议

不要直接暴露 HTTP 管理入口。用 Nginx 做 HTTPS 反向代理：

```nginx
server {
    listen 80;
    server_name axonhub.example.com;

    location / {
        proxy_pass http://127.0.0.1:18090;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

再配上 HTTPS 证书。



## 接入 cc-switch

AxonHub 部署好后，可以在 cc-switch 里为 Claude Code、Codex、Gemini 分别创建指向 AxonHub 的自定义 provider，实现统一代理和切换。

### 前置条件

- AxonHub 已部署并正常运行（`curl -i http://localhost:18090/health` 返回 200）
- 已在 AxonHub 管理界面创建了 API Key
- cc-switch 已安装

### 在 AxonHub 管理界面准备

1. 打开 `http://localhost:18090`
2. 进入通道管理，添加 Claude Code / Codex / Gemini 对应的上游供应商（如 OpenAI、Anthropic、Gemini），填入 API Key
3. 进入 API Key 管理，创建一个新的 API Key，记下这个 Key

### Claude Code 接入

在 cc-switch 中：

1. 新建自定义 provider，命名如 `AxonHub-Claude`
2. base URL 设为：

```text
http://localhost:8090/anthropic
```

3. API Key 填 AxonHub 的 Key

4. 选择 Claude Code 类型

5. 保存，切换到这个 provider

6. 终端里运行 `claude`，发送请求验证

   ccswitch中的请求地址为axonhub中的，apikey就是你创建的key

   ```
   # In your terminal, set the API key
   export ANTHROPIC_AUTH_TOKEN="ah-...04e0"
   export ANTHROPIC_BASE_URL="http://localhost:18090/anthropic"
   ```

   ![image-20260512104538520](https://wanwurong.oss-cn-beijing.aliyuncs.com/pic/image-20260512104538520.png)

### Codex 接入

1. 新建自定义 provider，命名如 `AxonHub-Codex`
2. base URL 设为：

```text
http://localhost:18090/v1
```

3. API Key 填 AxonHub 的 Key
4. 选择 Codex 类型
5. 保存，切换到这个 provider
6. 终端里运行 `codex`，发送请求验证

![image-20260512104620032](https://wanwurong.oss-cn-beijing.aliyuncs.com/pic/image-20260512104620032.png)

### Gemini CLI 接入

1. 新建自定义 provider，命名如 `AxonHub-Gemini`
2. base URL 设为 AxonHub 的 Gemini 兼容入口（需确认 AxonHub 当前版本是否支持 Gemini API 格式）
3. API Key 填 AxonHub 的 Key
4. 选择 Gemini 类型
5. 保存，切换到这个 provider
6. 终端里运行 `gemini`，发送请求验证

### 验证是否走通

```bash
# 确认 cc-switch 已切到 AxonHub provider
# 重启终端，打开对应 CLI
claude
# 或
codex
```

问一句：

```text
what model are you using right now?
```

然后去 AxonHub 管理界面看请求日志，确认请求确实经过了 AxonHub。

---