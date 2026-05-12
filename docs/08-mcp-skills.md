# 必装 MCP 与 Skills

## MCP 是什么

MCP（Model Context Protocol）是把 AI 客户端连接到外部工具、数据源和服务的标准协议。简单说：**MCP 让 Claude Code / Codex / Gemini 能读写文件、搜文档、查数据库、操作浏览器**。

MCP Router 是一个帮你集中管理这些 MCP 服务器的桌面应用：[https://github.com/mcp-router/mcp-router](https://github.com/mcp-router/mcp-router)

## 必装 MCP 服务器（按优先级）

以下安装命令使用 `claude mcp add`，Codex / Gemini 的添加方式见各自文档。

### 第一梯队：核心必备

**1. Context7 — 实时文档**

消灭"幻觉 API"，拉取最新版本文档。

```bash
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
```

**2. GitHub MCP — 代码协作**

```bash
claude mcp add-json github '{"type":"http","url":"https://api.githubcopilot.com/mcp/","headers":{"Authorization":"Bearer YOUR_GITHUB_PAT"}}'
```

**3. Playwright MCP — 浏览器自动化**

让 AI 能自己打开网页看结果。

```bash
claude mcp add playwright -- npx @playwright/mcp@latest
```

**4. Filesystem MCP — 安全文件读写**

官方文件系统访问，支持目录沙箱。

### 第二梯队：数据库与搜索

**5. Supabase MCP — 全栈数据库**

```bash
claude mcp add --transport http supabase https://mcp.supabase.com/mcp
```

**6. PostgreSQL MCP — 只读数据库查询**

```bash
claude mcp add postgres -- npx -y @modelcontextprotocol/server-postgres "postgresql://user:pass@localhost:5432/mydb"
```

**7. claude-context — 语义代码库搜索**

Zilliz 出品，BM25 + 向量混合检索，大项目必备。

```bash
npm install -g @zilliztech/claude-context
claude-context init
cd your-project && claude-context index .
claude mcp add claude-context -- npx -y @zilliztech/claude-context serve
```

### 第三梯队：记忆与推理

**8. Memory MCP — 跨会话记忆**

```bash
claude mcp add memory -- npx -y @modelcontextprotocol/server-memory
```

**9. Sequential Thinking MCP — 结构化推理**

强制 AI 分步思考→修正→收敛，适合架构设计。

```bash
claude mcp add sequential-thinking -- npx -y @modelcontextprotocol/server-sequential-thinking
```

### 按需安装：生产力工具

| MCP | 用途 | 安装方式 |
|-----|------|---------|
| Firecrawl | 网页抓取为 Markdown | `npx -y firecrawl-mcp` |
| Notion | 文档/数据库操作 | `https://mcp.notion.com/mcp` |
| Slack | 频道读取/消息发送 | 自建 Slack MCP URL |
| Linear | Issue 追踪 | `npx -y @linear/mcp` |
| Figma | 设计稿转代码 | Figma MCP 插件 |
| Semgrep | 安全扫描 (2000+ 规则) | `npx -y @semgrep/mcp` |
| Stripe | 支付/订阅管理 | Stripe MCP |
| Vercel | 部署/环境变量 | Vercel MCP |

## 推荐新手 Starter Stack

- **前端**：`filesystem + github + playwright + figma`
- **后端**：`git + postgres + context7`
- **全栈**：`filesystem + github + supabase + playwright + context7`

建议先从 3-5 个开始，不要一次装太多（会影响 token 消耗）。

## 必装 Skills

Skills 是可复用的 AI 编码能力包，安装后直接增强 AI 工具的行为。

### 1. Karpathy 编码指南

仓库：[https://github.com/forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills)

一个 `CLAUDE.md` 文件，四条原则：

| 原则 | 解决的问题 |
|------|-----------|
| 先思考再编码 | AI 的隐性假设、缺少权衡分析 |
| 保持简洁 | 过度复杂化、臃肿抽象 |
| 精准修改 | 改不该改的代码 |
| 目标驱动 | 没定义成功标准、改完不验证 |

**使用**：把 `CLAUDE.md` 放到项目根目录即可。

### 2. Everything Claude Code

仓库：[https://github.com/affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code)

140K+ stars，一套完整的 AI agent 优化系统，支持 Claude Code / Codex / Cursor / OpenCode / Gemini。包含 skills、hooks、安全扫描、token 优化、memory 持久化、子代理编排。

**安装**：按需选择性安装，参考仓库的 Shorthand Guide。

### 3. skills.sh

网站：[https://skills.sh/](https://skills.sh/)

Skills 的"应用商店"。支持 Claude Code、Codex、Cursor、Gemini、Copilot 等。

```bash
npx skills init        # 初始化
npx skills find <query> # 搜索 skills
```

热门 Skills：

| Skill | 来源 | 用途 |
|-------|------|------|
| find-skills | vercel-labs | 搜索发现 skills |
| frontend-design | anthropics | 前端设计规范 |
| brainstorming | obra | 需求厘清 |
| ui-ux-pro-max | nextlevelbuilder | UI/UX 增强 |
| agent-browser | vercel-labs | 浏览器自动化 |
| supabase-postgres | supabase | PG 最佳实践 |

## 推荐安装顺序

```bash
# 1. MCP（先装核心 3 个）
claude mcp add context7 -- npx -y @upstash/context7-mcp@latest
claude mcp add playwright -- npx @playwright/mcp@latest
claude mcp add memory -- npx -y @modelcontextprotocol/server-memory

# 2. Skills
npx skills init
npx skills find frontend-design
npx skills find brainstorming

# 3. Karpathy CLAUDE.md
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md

# 4. 按需安装更多 MCP 和 skills
```

---