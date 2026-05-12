# Claude Code

## 它是什么

Claude Code 是 Anthropic 的 AI 编码助手，可以读代码库、编辑文件、运行命令，并和你的开发工具集成。你可以把它理解成一个“会看完整项目上下文的终端/IDE 助手”。

它适合这些场景：

- 读懂一个陌生项目
- 修 bug
- 批量改文件
- 跑测试并修复失败
- 写 commit message 或 PR 内容
- 用自然语言驱动代码工作流

## 它能在哪些地方用

Claude Code 不只是在终端里用，它还有多种形态：

- Terminal
- VS Code
- Desktop app
- Web
- JetBrains

如果你是新手，最稳妥的入口通常是 **Terminal**，因为最直接、最容易理解。

## 开始前先知道什么

- 大多数使用场景需要 Claude subscription 或 Anthropic Console 账号
- Terminal CLI 和 VS Code 也支持第三方提供方
- 官方推荐先在你的项目目录里启动，再让它看当前代码库

## 安装

### macOS / Linux / WSL

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### Windows PowerShell

```powershell
irm https://claude.ai/install.ps1 | iex
```

### Windows CMD

```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

### Homebrew

```bash
brew install --cask claude-code
```

### WinGet

```powershell
winget install Anthropic.ClaudeCode
```

### Linux 包管理器

官方还提供 apt、dnf、apk 等安装方式，适合 Debian、Fedora、RHEL 和 Alpine。

### Windows 小提示

- 如果你在 native Windows 上用 Claude Code，建议装 Git for Windows，这样它更容易使用 Bash tool
- 如果没装 Git for Windows，Claude Code 会改用 PowerShell 作为 shell tool
- WSL 环境不需要 Git for Windows

## 安装后怎么启动

进入你的项目目录，然后运行：

```bash
cd your-project
claude
```

首次使用时会提示你登录。

## 登录 / 认证

Claude Code 会在第一次启动时引导你登录。对新手来说，最重要的是：

- 先用官方推荐方式登录
- 不要一开始就折腾复杂的 API 配置
- 如果你用的是第三方提供方，先确认当前 surface 是否支持

## 你可以怎么用它

### 1. 让它直接做任务

```bash
claude "write tests for the auth module, run them, and fix any failures"
```

### 2. 让它进入交互式项目工作流

你可以让它：

- 概览项目结构
- 找入口文件
- 解释某个报错
- 修改多个文件并验证结果

### 3. 让它做批处理或脚本化工作

```bash
tail -200 app.log | claude -p "Slack me if you see any anomalies"
```

```bash
git diff main --name-only | claude -p "review these changed files for security issues"
```

## 核心能力

Claude Code 的常见能力可以理解成下面几类：

- **Build features and fix bugs**：根据自然语言规划并修改代码
- **Automate repetitive work**：补测试、修 lint、处理 merge conflict、更新依赖
- **Work with git**：可以做 commit、分支、PR 相关工作
- **Connect tools with MCP**：接外部数据源或工具
- **Customize with CLAUDE.md / skills / hooks**：按项目规则定制行为
- **Run agent teams**：把任务拆成多个子任务并行处理

## 关键概念

### CLAUDE.md

`CLAUDE.md` 是放在项目根目录的 markdown 文件，Claude Code 会在每次会话开始时读取它。你可以用它记录：

- 编码规范
- 架构约定
- 常用命令
- 代码审查清单

### Skills

Skills 用来把可重复的工作流程打包成可复用能力，比如：

- `/review-pr`
- `/deploy-staging`

### Hooks

Hooks 可以在 Claude Code 动作前后自动执行 shell 命令，比如：

- 编辑后自动格式化
- 提交前跑 lint

### MCP

MCP（Model Context Protocol）是把 Claude Code 接到外部工具和数据源的标准方式。比如它可以读取 Google Drive 文档、更新 Jira 任务、读取 Slack 数据，或者接你自己的内部工具。

### Auto memory

Claude Code 会把一些可复用的项目知识保存成 memory，比如：

- 构建命令
- 调试经验
- 项目偏好

这样下一次会话就能继续沿用。

## 推荐的新手流程

建议不要把 Claude Code 当成“随便问一句就让它改代码”的工具，而是让它围绕 Trellis 任务工作：

1. 先进入项目目录
2. 启动 `claude`
3. 让它先概览项目，不要修改文件
4. 创建或确认 Trellis 任务
5. 在 planning 阶段整理需求和验收标准
6. 再让它解释具体文件或报错
7. 只做一个小修复
8. 运行测试、lint 或 typecheck 验证结果
9. 把新的经验更新到文档或 Trellis spec

Windows PowerShell 示例：

```powershell
cd C:\code\my-project
claude
```

进入后可以先问：

```text
请先不要修改文件。请概览这个项目结构，说明主要模块、启动方式、测试方式，并告诉我建议先阅读哪些文件。
```

然后再问：

```text
请按 Trellis 工作流处理这个任务：先规划，不要直接改代码；需求和验收标准明确后再实现；实现后运行检查并总结结果。
```

## 验证

安装成功后，你应该能看到：

- `claude` 能正常启动
- 能识别当前项目目录
- 首次启动会要求登录
- 能接受自然语言任务
- 能读写文件或运行命令

## 常见坑

- 没在项目目录里启动
- 把登录、API Key、第三方 provider 混为一谈
- 以为所有平台的安装命令完全一致
- Windows 的 PowerShell / CMD 命令写混
- 忘了先装 Git for Windows 就期待 Bash tool

## 平台差异

- **Windows**：优先用 PowerShell / WinGet / native install，注意 CMD 和 PowerShell 语法差异
- **macOS**：可以用 native install 或 Homebrew
- **Linux**：可以用 native install 或包管理器
- **WSL**：和 Linux 路线基本一致

## 与 Trellis 配合

Claude Code 最适合做完整的 Trellis 流程，因为它能读写文件、运行命令、检查 git diff。

推荐口令：

```text
请按 Trellis 流程处理这个任务：先规划，不要直接改代码；PRD 明确后再实现；实现后运行检查；最后总结验证结果。
```

如果你要先概览项目，可以这样问：

```text
请先不要修改文件。请概览这个项目结构、主要模块、启动方式、测试方式，并告诉我建议先读哪些文件。
```

如果你想让 Claude Code 直接沿着 Trellis 任务执行，可以这样说：

```text
当前项目使用 Trellis 工作流。请先读取任务 PRD 和相关 spec，只实现当前任务范围，不要修改无关文件。完成后运行测试并说明结果。
```

## 进一步阅读

- Claude Code overview: https://code.claude.com/docs/en/overview
- Claude Code setup: https://code.claude.com/docs/en/setup
- Quickstart: https://code.claude.com/docs/en/quickstart
- Common workflows: https://code.claude.com/docs/en/common-workflows
- Best practices: https://code.claude.com/docs/en/best-practices
- Settings: https://code.claude.com/docs/en/settings
- Troubleshooting: https://code.claude.com/docs/en/troubleshooting

---

