# Trellis 工作流：安装与第一个任务

## 它是什么

Trellis 不是另一个 AI 模型，也不是替代 Claude Code、Codex 或 Gemini 的工具。它是一套 AI 编码项目工作流，把“需求 → 计划 → 实现 → 检查 → 收尾”落到项目文件里。

新手先记住一句话：

> Trellis 管流程和上下文，Claude Code / Codex / Gemini 负责具体执行。

---

## 1. 安装 Trellis

### 系统要求

Trellis 支持：

- macOS
- Linux
- Windows

需要提前安装：

- Node.js 18+
- Python 3.9+

### 安装命令

全局安装：

```bash
npm install -g @mindfoldhq/trellis
```

如果你要安装 beta 版本：

```bash
npm install -g @mindfoldhq/trellis@beta
```

检查版本：

```bash
trellis --version
```

---

## 2. 进入你的项目目录

### Windows PowerShell

假设你的项目在：

```text
C:\code\my-project
```

执行：

```powershell
cd C:\code\my-project
```

确认当前目录：

```powershell
pwd
```

### macOS / Linux

```bash
cd ~/code/my-project
pwd
```

---

## 3. 初始化 Trellis

Trellis 的初始化命令是：

```bash
trellis init -u your-name
```

这里的 `your-name` 会成为你的开发者身份，并在项目里创建类似这样的个人工作区：

```text
.trellis/workspace/your-name/
```

### 给 Claude Code 初始化

如果你主要用 Claude Code：

```bash
trellis init -u your-name --claude
```

### 同时给多个工具初始化

如果你同时用 Claude Code、Codex、Gemini：

```bash
trellis init -u your-name --claude --codex --gemini
```

### 交互式初始化

如果你不确定要配置哪些平台，可以直接运行：

```bash
trellis init -u your-name
```

Trellis 会检测已安装的平台，并询问你要配置哪些。

---

## 4. 常见平台 flag

Trellis 支持多个 AI 编码平台，常见 flag 包括：

```text
--claude
--cursor
--opencode
--codex
--gemini
--qoder
--codebuddy
--copilot
--droid
--pi
--antigravity
--windsurf
--kilo
```

新手最常用的是：

```bash
trellis init -u your-name --claude --codex --gemini
```

---

## 5. 初始化后会生成什么

以 Claude Code 为例，初始化后项目里会出现：

```text
your-project/
├── .trellis/              # Trellis 核心目录
│   ├── .developer         # 当前开发者身份，通常不进 git
│   ├── workflow.md        # 工作流说明
│   ├── config.yaml        # 项目配置
│   ├── spec/              # 项目规范
│   ├── tasks/             # 任务目录
│   └── workspace/         # 开发者工作区
├── .claude/               # Claude Code 配置、命令、技能、hooks
└── .agents/skills/        # 跨平台共享 skills
```

不同平台会写入不同配置目录，例如：

- Claude Code：`.claude/commands/trellis/`、`agents/`、`skills/`、`hooks/`
- Codex：`.codex/agents/`、`skills/`、`hooks/`，以及仓库根目录 `AGENTS.md`
- Gemini CLI：`.gemini/commands/trellis/`、`agents/`、`skills/`、`hooks/`

但 `.trellis/` 是跨平台一致的核心目录。

---

## 6. 第一个任务怎么跑

初始化完成后，不要急着让 AI 直接改代码。推荐先打开你的 AI 编码工具。

### Claude Code

```bash
claude
```

### Codex

```bash
codex
```

### Gemini CLI

```bash
gemini
```

然后直接描述你要做什么，例如：

```text
新增用户登录功能
```

Trellis 会引导 AI 按下面流程走：

```text
Plan → Execute → Finish
```

也就是：

1. 先规划需求
2. 再执行实现
3. 最后检查和收尾

---

## 7. 第一次用时推荐这样问 AI

你可以在 Claude Code / Codex / Gemini 里这样说：

```text
请按 Trellis 工作流处理这个任务。先不要直接改代码，先帮我澄清需求并整理计划；计划确认后再执行；执行后运行检查并总结结果。
```

如果你还不熟悉项目，可以先问：

```text
请先不要修改文件。请概览这个项目结构，说明主要模块、启动方式、测试方式，并告诉我建议先阅读哪些文件。
```

---

## 8. 完成任务后怎么收尾

当任务做完、你也测试确认没问题后，在支持 Trellis 命令的平台里运行：

```text
/trellis:finish-work
```

如果你觉得当前流程还没走完，需要继续推进：

```text
/trellis:continue
```

注意：Agent-capable 平台通常会在会话开始时自动加载 workflow 和上下文；有些平台不需要你手动运行 `/trellis:start`。

---

## 9. 新手最小实践流程

如果你只想快速上手，照这个来：

```bash
# 1. 安装 Trellis
npm install -g @mindfoldhq/trellis

# 2. 进入项目
cd your-project

# 3. 初始化 Claude Code + Codex + Gemini
trellis init -u your-name --claude --codex --gemini

# 4. 打开 Claude Code
claude
```

然后对 Claude Code 说：

```text
请按 Trellis 工作流帮我完成一个小任务：先规划，再实现，再检查。任务是：<你的任务描述>
```

---

## 10. 进阶：Trellis 内部脚本命令

下面这些命令更偏进阶或调试。新手不需要一开始就记住。

```bash
python ./.trellis/scripts/task.py create "<task title>" --slug <name>
python ./.trellis/scripts/task.py current --source
python ./.trellis/scripts/get_context.py --mode packages
python ./.trellis/scripts/get_context.py --mode phase --step 1.1
python ./.trellis/scripts/task.py add-context "<TASK_DIR>" implement "<path>" "<reason>"
python ./.trellis/scripts/task.py add-context "<TASK_DIR>" check "<path>" "<reason>"
python ./.trellis/scripts/task.py start <task-dir>
python ./.trellis/scripts/task.py validate <name>
python ./.trellis/scripts/task.py finish
python ./.trellis/scripts/task.py archive <name>
```

建议新手优先使用 `trellis init` 和 `/trellis:continue`、`/trellis:finish-work` 这类高层入口；等熟悉后再看内部脚本。

---

## 11. 常见坑

- 没装 Node.js 18+ 或 Python 3.9+
- 忘了先 `cd` 到项目目录
- `trellis init` 没带 `-u your-name`
- 只初始化了 Claude Code，却想在 Codex / Gemini 里直接用同样配置
- 一上来就研究内部 `task.py`，反而把新手流程弄复杂
- AI 还没规划就让它直接改代码

---

## 12. 一句话总结

> 新手先记住：`npm install -g @mindfoldhq/trellis` → `cd your-project` → `trellis init -u your-name --claude --codex --gemini` → 打开 Claude Code / Codex / Gemini 描述任务 → 用 `/trellis:finish-work` 收尾。

---
