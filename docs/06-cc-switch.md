# cc-switch

## 它是什么

cc-switch 是一个桌面端管理工具，目标是把多个 AI CLI 的配置、切换和部分日常管理集中到一个界面里。它不是替代 Claude Code、Codex、Gemini CLI 本身，而是帮你更方便地管理它们。

根据仓库说明，它支持的工具包括：

- Claude Code
- Codex
- Gemini CLI
- OpenCode
- OpenClaw
- Hermes Agent

如果你经常在多个 CLI 之间切换，或者想统一管理 provider、MCP、skills 和会话记录，这个工具会很有用。

## 它适合解决什么问题

新手最容易遇到的痛点通常不是“工具不会用”，而是：

- 每个 CLI 的配置入口都不一样
- 多个账号 / provider 切换后容易记错
- 某些设置散落在不同目录里，迁移困难
- 想备份、同步、恢复配置时很麻烦

cc-switch 的作用，就是把这些杂项收拢到一个地方。

## 安装

仓库说明里给出的常见安装方式如下。

### Windows

- 下载最新的 `.msi`：[前往 cc-switch Releases](https://github.com/farion1231/cc-switch/releases/latest)
- 或下载 portable `.zip`：[前往 cc-switch Releases](https://github.com/farion1231/cc-switch/releases/latest)

### macOS

- 推荐使用 Homebrew：

```bash
brew tap farion1231/ccswitch
brew install --cask cc-switch
```

- 更新：

```bash
brew upgrade --cask cc-switch
```

- 或从 [cc-switch Releases](https://github.com/farion1231/cc-switch/releases/latest) 下载 `.dmg` / `.zip`。

### Linux

- 从 [cc-switch Releases](https://github.com/farion1231/cc-switch/releases/latest) 下载 `.deb`
- 从 [cc-switch Releases](https://github.com/farion1231/cc-switch/releases/latest) 下载 `.rpm`
- 从 [cc-switch Releases](https://github.com/farion1231/cc-switch/releases/latest) 下载 `.AppImage`
- Arch Linux 可用 `paru -S cc-switch-bin`

## 首次启动会发生什么

第一次打开时，cc-switch 通常会让你先把已有 CLI 配置导入进来，或者直接创建一个默认 provider。

对新手来说，最稳妥的顺序是：

1. 先打开 cc-switch
2. 导入已有 CLI 配置，或新建一个 provider
3. 检查默认 provider 是否正确
4. 再去切换到其他 provider 试一次

如果你本来就已经在终端里配置过 Claude Code、Codex 或 Gemini CLI，这个导入步骤会省很多时间。

## 它怎么组织配置

仓库说明里提到，它有一个 SQLite 数据库作为主数据源，主要位置包括：

- 数据库：`~/.cc-switch/cc-switch.db`
- 本地设置：`~/.cc-switch/settings.json`
- 备份：`~/.cc-switch/backups/`
- Skills：`~/.cc-switch/skills/`
- Skills 备份：`~/.cc-switch/skill-backups/`

如果你换电脑，或者担心设置丢失，这些路径就是重点。

## Provider 和切换逻辑

cc-switch 的核心概念可以简单理解成：

- **provider**：某个具体 AI CLI 的配置集合
- **切换**：把当前默认配置切到另一个 provider
- **恢复官方登录**：必要时切回官方登录或官方 preset

仓库说明提到，它支持：

- 内置 provider presets
- 自定义 provider
- 首次启动导入已有 CLI 配置

对新手来说，最常见的用法就是先保留官方 preset，再自己新增一个配置做试验。

## 切换后要不要重启终端

一般情况下，仓库说明提到：

- 大多数 CLI 切换后需要重启终端或重新打开工具
- Claude Code 是一个例外，仓库说明里特别提到它的切换行为不同

所以你可以把经验记成一句话：

> 切换完先重开终端，再验证是否真的生效。

## 官方登录怎么保留

如果你担心自己配乱了，可以保留一个“官方登录”路径：

- 在 cc-switch 里添加官方 preset
- 用它提供的登录流重新登录
- 需要时切回原来的 provider

这对新手特别重要，因为它相当于一个“保险开关”。

## 备份与同步

仓库说明里提到支持云同步，包含：

- Dropbox
- OneDrive
- iCloud
- WebDAV

这表示你的本地配置不一定只能存在单机上。如果你经常换电脑，或想做跨设备同步，这一块值得认真配置。

## MCP、Skills 和会话管理

仓库说明提到 cc-switch 不只是改 provider，还会管理：

- MCP servers
- prompts
- skills
- sessions
- usage tracking

---
