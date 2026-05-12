# 用 CLIProxyAPI 代理第三方 API，并接入 cc-switch

## 目标

这一章讲的是一个组合用法：

1. 用 CLIProxyAPI（下面简称 CPA）作为第三方 API / 多账号 / 多模型代理层
2. 在 CPA 里统一管理上游账号或 provider
3. 再通过 cc-switch 把 Claude Code、Codex、Gemini 等 CLI 的配置切到 CPA
4. 最后让不同 CLI 都走同一个代理入口

你可以把它理解成：

> CPA 负责“代理和路由 API”，cc-switch 负责“切换本地 CLI 配置”。

## 整体架构

```text
Claude Code / Codex / Gemini CLI
        ↓
cc-switch 切换本地 CLI provider 配置
        ↓
CLIProxyAPI / CPA 代理服务
        ↓
第三方 API / 多账号 / 上游模型服务
```

这里最容易混淆的是：

- CPA 是服务端代理
- cc-switch 是本地桌面配置管理工具
- Claude Code / Codex / Gemini CLI 是实际发起请求的客户端

## 适合谁

这个方案适合：

- 你有多个第三方 API 上游
- 你想把多个 CLI 都统一接到一个代理地址
- 你想做多账号路由、负载均衡或故障切换
- 你不想每个 CLI 都单独维护一套复杂配置

如果你只是单账号、单工具使用，其实不一定需要这套组合。

## 第一步：部署 CLIProxyAPI / CPA

CPA 仓库地址：

```text
https://github.com/router-for-me/CLIProxyAPI
```

官方帮助站：

```text
https://help.router-for.me/
https://help.router-for.me/management/api
```

### 方式 A：可执行文件运行

仓库可见信息里没有完整列出每个平台的二进制运行命令，所以建议按官方帮助站或 Releases 的当前版本说明执行。

通用步骤可以写成：

1. 打开 CPA GitHub Releases 或官方帮助站
2. 下载当前系统对应的可执行文件
3. 解压到一个固定目录
4. 准备配置文件或环境变量
5. 启动 CPA 服务
6. 记录监听地址，例如：

```text
http://127.0.0.1:你的端口
```

### 方式 B：Docker 运行

仓库里有 Docker 相关文件，例如：

- `Dockerfile`
- `docker-compose.yml`
- `docker-build.sh`
- `docker-build.ps1`

因此更推荐新手优先走 Docker Compose，因为更容易迁移和重启。

通用步骤：

```bash
git clone https://github.com/router-for-me/CLIProxyAPI.git
cd CLIProxyAPI
```

然后按仓库当前 `docker-compose.yml` 准备环境变量或配置文件，再启动：

```bash
docker compose up -d
```

如果你的 Docker 版本较旧，可以用：

```bash
docker-compose up -d
```

检查运行状态：

```bash
docker compose ps
```

查看日志：

```bash
docker compose logs -f
```

## 第二步：在 CPA 里配置第三方 API 上游

CPA 的核心价值是把不同 CLI 和上游 API 统一起来。你需要在 CPA 的配置或管理界面中加入：

- 上游 API 地址
- API Key 或 OAuth 账号
- 模型名称映射
- 路由规则
- 多账号轮询或负载均衡策略

注意：不同版本的 CPA 配置项可能会变化，所以这里不要硬背字段名，应该以官方帮助站和当前配置模板为准。

建议你先只配置一个上游，确认能通，再加多个账号。

## 第三步：验证 CPA 是否可用

在接入 cc-switch 前，先单独验证 CPA。

你至少要确认：

- CPA 服务已经启动
- 监听端口正确
- 管理接口或健康检查能访问
- 能成功代理一个最小请求
- 日志里能看到请求记录

如果这一步不通，不要急着配置 cc-switch。先把 CPA 修好。

## 第四步：在 cc-switch 中添加自定义 provider

打开 cc-switch 后，建议按这个顺序做：

1. 新建一个自定义 provider
2. 命名为 `CPA`、`CLIProxyAPI` 或你能看懂的名字
3. 把 base URL 指向 CPA 的地址
4. 填入 CPA 要求的 API Key 或 token
5. 选择对应的 CLI 类型，例如 Claude Code、Codex、Gemini
6. 保存配置

这里的关键是：

```text
CLI 的请求地址 → 不再直接指向官方 API → 改为指向 CPA
```

## 第五步：分别配置 Claude Code / Codex / Gemini

### Claude Code

Claude Code 官方文档提到 Terminal CLI 和 VS Code 支持 third-party providers。你可以在 cc-switch 里创建 Claude Code 对应的 provider，把它指向 CPA。

建议流程：

1. 先保留一个官方 Claude Code provider
2. 新增一个 CPA provider
3. 切换到 CPA provider
4. 回到终端运行：

```bash
claude
```

5. 发一个简单请求确认是否走 CPA

### Codex

Codex 可以通过 ChatGPT 登录或 API Key 路线使用。接 CPA 时，重点是让 Codex 的 API 请求指向 CPA 提供的兼容接口。

建议流程：

1. 先确认 CPA 提供 OpenAI / Codex 兼容接口
2. 在 cc-switch 中新增 Codex provider
3. base URL 指向 CPA
4. token / key 填 CPA 对应凭据
5. 切换后运行：

```bash
codex
```

### Gemini CLI

Gemini CLI 支持 Google 登录、API Key 或 Vertex AI 等方式。接 CPA 时，重点是确认 CPA 是否提供 Gemini 兼容接口。

建议流程：

1. 在 CPA 中配置 Gemini 兼容上游
2. 在 cc-switch 中新增 Gemini provider
3. base URL 指向 CPA 的 Gemini 兼容入口
4. 切换后运行：

```bash
gemini
```

## 第六步：切换后验证

每切换一次 provider，都建议做三件事：

1. 重启终端或重新打开对应 CLI
2. 发一个最小请求
3. 看 CPA 日志里有没有请求进入

例如你可以问：

```text
hello, tell me which model/provider you are using
```

不要只看 CLI 有没有响应，还要看 CPA 日志。日志能证明请求确实经过了代理。

## 一句话总结

> CPA 管 API 代理和上游路由，cc-switch 管本地 CLI 配置切换。先跑通 CPA，再把 Claude Code、Codex、Gemini 的 provider 指向 CPA。

---
