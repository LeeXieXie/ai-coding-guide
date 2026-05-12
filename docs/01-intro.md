# 开始之前

## 这份指南涵盖什么

- Claude Code、Codex、Gemini CLI 的安装与使用
- cc-switch 统一管理多个 AI CLI 的 provider 配置
- Trellis 工作流：让 AI 编码有章可循
- AxonHub / CLIProxyAPI / ccLoad：代理、网关、负载均衡
- 必装 MCP 与 Skills 推荐
- 所有工具如何串联成一套完整的 AI 编码工具链

## 阅读顺序

从头到尾按章节读即可，每一章都尽量独立。如果你已经有某个工具在用，可以直接跳到对应章节。

## 通用准备

- 终端：Windows Terminal / PowerShell / macOS Terminal / Linux Shell
- 网络能访问各工具的官方服务
- Docker（网关和代理工具需要）

## Windows 特别说明

- Windows 10 或 11
- 推荐用 [Scoop](01-scoop.md) 安装 Node.js 和 Git，一行命令搞定
- 如果工具要求 Node.js，装 Node.js LTS
- 优先用官方发布包，不要依赖第三方镜像
