# kickstart.pi

一个注释详尽、开箱即用的 [Pi](https://github.com/earendil-works/pi-mono) 配置起点，让你真正理解它在做什么——来自一份真实日常使用的配置。

> 这里只收录本配置实际在用的组件，每个组件一份简短安装指南，全部为**全局安装**。

[English](README.md) | [简体中文](README.zh-cn.md)

---

## 目录

- [设计理念](#设计理念)
- [快速开始](#快速开始)
- [项目结构](#项目结构)
- [如何让它为你所用](#如何让它为你所用)
- [这不是什么](#这不是什么)
- [内置](#内置)
- [美化](#美化)
- [节省 token](#节省-token)
- [Agentic 工作流](#agentic-工作流)
- [会话交互](#会话交互)
- [代码智能](#代码智能)
- [附加功能](#附加功能)
- [AGENTS.md](#agentsmd)
- [致谢](#致谢)

---

## 设计理念

大部分"AI 配置起点"仓库给你的是一个成品。你拿到了能力，但不知道每一步在做什么、为什么这么做。

**`kickstart.pi` 反过来**：

- 每份指南都短小、只讲一个组件
- 每个决定都解释原因，不只是展示结果
- 只收录本配置实际使用的组件——不堆砌用不上的清单
- 全部**全局安装**（`~/.pi/agent/`），一次安装覆盖所有项目
- 它是起点，不是框架——添加自己的设置，删掉用不到的

---

## 快速开始

**让 pi 帮你安装**（推荐）

把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation.md
```

**手动安装**

> ⚠️ 如果 `~/.pi/agent/` 已有配置，请先备份。下面的 `cp -an` 只补充不存在的文件，不会覆盖。

```bash
mkdir -p ~/.pi/agent
repo_dir="$(mktemp -d)"
git clone https://github.com/fengye0926/kickstart.pi "$repo_dir"
cp -an "$repo_dir"/. ~/.pi/agent/
rm -rf "$repo_dir"
```

之后在任何项目目录下启动 pi：

```bash
cd /path/to/project
pi
```

---

## 项目结构

```
kickstart.pi/
│
├── LICENSE
├── README.md            ← 你正在阅读的英文版
├── README.zh-cn.md      ← 你正在阅读的这份
├── skills/
│   └── frontend-image-to-code/
│       └── SKILL.md     ← 内置技能：设计图 → 可用的前端代码
└── docs/
    ├── installation.md                       ← MCP 配置：内置 MCP + exa / context7 / searchcode
    ├── installation-open-tui.md              ← pi-open-tui 界面美化（全局）
    ├── installation-rtk.md                   ← 省 token 的 bash 改写器（全局）
    ├── installation-matt-pocock-skills.md    ← 工程技能集（全局）
    ├── installation-subagents.md             ← @tintinweb/pi-subagents（全局）
    ├── installation-permission-system.md     ← 工具、bash、MCP、skill 的权限闸门（全局）
    ├── installation-rpiv-todo.md             ← 常驻任务面板（全局）
    ├── installation-ask-user-question.md     ← 结构化提问弹窗（全局）
    ├── installation-pi-lsp.md                ← 定向 LSP 诊断与修复（全局）
    ├── installation-agent-browser.md         ← 浏览器自动化技能（全局）
    └── installation-frontend-image-to-code.md ← 内置技能（全局）
```

就这些。没有 `settings.json`、没有 agent、没有内置扩展——只有 [`docs/`](docs) 下的指南和一个示例技能。唯一必须配置的是三个 MCP 服务器，见 [`docs/installation.md`](docs/installation.md) 的 Step 2（`context7`、`searchcode`、`exa`）——Pi 原生支持 MCP，不需要 adapter 包。

---

## 如何让它为你所用

1. **通读 `README.md`** —— 理解设计理念与可用组件。
2. **决定 `~/.pi/agent/settings.json` 里放什么** —— 本仓库有意不带配置。选好 provider、model、theme；pi 首次启动会引导你完成。
3. **配置三个 MCP 服务器** —— Pi 已原生支持 MCP；按 [`docs/installation.md`](docs/installation.md) 的 Step 2 和 Step 3 操作。全局 `AGENTS.md` 引用的就是它们。
4. **按需安装全局组件** —— [pi-open-tui](docs/installation-open-tui.md)、[rtk](docs/installation-rtk.md)、[mattpocock/skills](docs/installation-matt-pocock-skills.md)、[pi-subagents](docs/installation-subagents.md)、[pi-permission-system](docs/installation-permission-system.md)、[rpiv-todo](docs/installation-rpiv-todo.md)、[ask-user-question](docs/installation-ask-user-question.md)、[pi-lsp](docs/installation-pi-lsp.md)、[agent-browser](docs/installation-agent-browser.md)，以及内置的 [frontend-image-to-code](docs/installation-frontend-image-to-code.md)。用 `pi list` 查看已装内容，用 `pi config` 启用或禁用资源。
5. **定制全局 `AGENTS.md`** —— MCP 使用提示、语言偏好、做事风格、技能路由。
6. **在需要的项目里加项目级 `AGENTS.md`** —— 项目结构、技术栈、编码规范。
7. **在 `.pi/prompts/` 里写自己的 prompt 模板** —— 把重复流程做成 `/your-command` 斜杠命令。

用不到的，删掉。它是起点，不是框架。

---

## 这不是什么

- 不是多 agent 编排系统
- 不是可投产的 AI 流水线
- 不是不需要读就能用的东西

---

## 内置

[`docs/installation.md`](docs/installation.md) 的安装流程把本仓库开箱即用所需的东西都装好：

> **MCP 已内置于 Pi。** Pi 0.99+ 原生支持 MCP，不再需要 adapter 包。这套配置在 `~/.pi/agent/mcp.json` 里维护三个服务器，让 agent 能接入更广的工具生态。

**三个 MCP 服务器**——与安装指南 Step 2 完全一致：

- **context7** — 库和框架文档查询（direct）
- **searchcode** — 公开仓库代码搜索（deferred）
- **exa** — 网页搜索（direct）

`pi mcp list` 检查连接状态；会话里用 `/mcp` 打开同一视图，可开关服务器或调整 exposure。

**不附带 `settings.json`**——pi 首次启动时会引导你选 provider / model / theme。如果已有备份，可以用 `cp ~/.pi/agent.bak/settings.json ~/.pi/agent/` 恢复。

---

## 美化

### pi-open-tui

[pi-open-tui](https://pi.dev/packages/pi-open-tui) 是一个精致的 pi TUI 样式扩展——动画头部、[Starship](https://starship.rs/) 风格的两行底栏（cwd、git 分支与状态、运行时版本、上下文条、模型、token、费用），圆角编辑器，以及每轮结束后显示的 turn 遥测（TPS / TTFT / 卡顿 / 费用）。在 pi 里用 `/open-tui` 就能配置。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-open-tui.md
```

装好后 `/open-tui` 配置头部、底栏分段、图标模式和遥测。

---

## 节省 token

### rtk

[rtk](https://github.com/rtk-ai/rtk) 把啰嗦的 shell 命令（`git status`、`pnpm list`、`vitest`、`cargo test` 等）透明改写成紧凑的省 token 形式。每个 pi 会话自动拦截改写——工作流不变，token 更少。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-rtk.md
```

`rtk init --agent pi --global` 会生成 `~/.pi/agent/extensions/rtk.ts`，pi 启动时自动发现。该扩展失败时放行：`rtk` 缺失、版本过旧或改写超时，原始命令照常执行。

---

## Agentic 工作流

### mattpocock/skills

[mattpocock/skills](https://github.com/mattpocock/skills) 是 Matt Pocock 维护的工程技能集——grilling 访谈、TDD、bug 诊断、代码审查、领域建模、研究等。技能按任务自动加载，其中一些还注册为 `/skill:<name>` 命令。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-matt-pocock-skills.md
```

技能会写入 `~/.agents/skills/` 并软链到 `~/.pi/agent/skills/`。每个仓库首次使用时运行一次 `/skill:setup-matt-pocock-skills`，选择 issue tracker、triage 标签，以及 `CONTEXT.md` / ADR 的存放位置。

---

## 会话交互

### rpiv-todo

[@juicesharp/rpiv-todo](https://www.npmjs.com/package/@juicesharp/rpiv-todo) 给模型一个你能看见的任务列表：`todo` 工具、`/todos` 命令，以及编辑器上方的实时面板，显示已完成、进行中和排队中的任务。列表从对话本身重建，`/reload` 和压缩之后依然存在。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-rpiv-todo.md
```

装好后 `/todos` 确认面板已加载。

### ask-user-question

[@juicesharp/rpiv-ask-user-question](https://www.npmjs.com/package/@juicesharp/rpiv-ask-user-question) 给模型一个工具——`ask_user_question`——弹出最多四个问题的终端对话框，带写好的选项，而不是替你猜。你可以用自己的话作答、给答案加备注，并排比较 preview。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-ask-user-question.md
```

装好后给模型一个埋着真实决策的任务，直接做选择，而不是事后返工。

---

## 代码智能

### pi-lsp

[@narumitw/pi-lsp](https://www.npmjs.com/package/@narumitw/pi-lsp) 在编辑过程中为 pi 提供定向的 LSP 诊断（`lsp_diagnostics`）和源码修复（`lsp_fix`）。语言服务器按“命令 + 文件扩展名”路由配置，而不是写死语言家族；只在匹配的 tool call 时启动，用完即关。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-pi-lsp.md
```

装好后 `/lsp` 查看配置的服务器命令在 `PATH` 里是否可用。

---

## 附加功能

### subagents

[@tintinweb/pi-subagents](https://pi.dev/packages/@tintinweb/pi-subagents) 为 pi 带来 Claude Code 风格的自主 sub-agent——在独立会话中派生专门的 agent，每个都有自己的工具、系统提示、模型和思考等级。支持前台 / 后台运行、中途介入，也可以通过 `.pi/agents/*.md`（项目级）或全局定义自己的 agent 类型。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-subagents.md
```

装好后 `/agents` 在 pi 里管理、查看、介入 sub-agent。

### pi-permission-system

[@gotgenes/pi-permission-system](https://pi.dev/packages/@gotgenes/pi-permission-system) 在 agent 与每次工具、bash、MCP、skill 调用之间放一道确定性权限闸门。三种状态（`allow` / `deny` / `ask`）加四层权限面（`path` → `external_directory` → 工具级规则 → `bash` 规则）覆盖了编码 agent 的大部分操作；没有预先放行的调用会弹出 UI 确认框。sub-agent 会话里的 `ask` 会转发到父会话，因此本配置的 [pi-subagents](#subagents) 操作走同一套策略。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-permission-system.md
```

装好后编辑 `~/.pi/agent/extensions/pi-permission-system/config.json` 写你的策略——先用指南里的安全默认（deny `.env`、bash 默认 ask、cwd 外目录 ask），再按需收紧。

### agent-browser

[agent-browser](https://github.com/vercel-labs/agent-browser) 通过 CDP（Chrome DevTools Protocol）让 agent 控制浏览器，比传统无头浏览器方案更省 token。它以全局技能形式安装，技能本身只是一个 stub，运行前从 `agent-browser` CLI 拉取当前版本的说明，因此不会过期。

建议**全局安装**。把下面这段粘贴到 pi：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-agent-browser.md
```

### frontend-image-to-code

本仓库内置一个技能：[`frontend-image-to-code`](skills/frontend-image-to-code/SKILL.md)。把截图、Mockup 或 Figma 导出图交给 agent，它会先解析出可实现规格，再映射到现有项目技术栈，完成实现，并用渲染结果对照参考图——不会自创另一套视觉方向。技能正文使用中文。

建议**全局安装**（在本仓库克隆目录里执行）：

```bash
mkdir -p ~/.pi/agent/skills
cp -R skills/frontend-image-to-code ~/.pi/agent/skills/
```

详细说明与验证方式见 [`docs/installation-frontend-image-to-code.md`](docs/installation-frontend-image-to-code.md)。

---

## AGENTS.md

AGENTS.md 是 pi 在每个会话里加载的全局指令文件。保持简短——只写真正跨项目都适用的内容。

pi 还会从 cwd 向上自动发现 `AGENTS.md` / `CLAUDE.md`，所以项目根的 `AGENTS.md` 会在该项目中覆盖全局。

**全局文件建议包含**：

- **MCP 使用提示** — 如 `Use exa for web search (current information, news, facts).`
- **语言偏好** — 如 `Reply in Chinese.`
- **个人编码偏好** — 如 `Never use any type. Prefer explicit types.`
- **做事风格** — 如 `Keep responses concise. No need to explain obvious steps.`
- **技能路由** — 如 `For screenshot → frontend code requests, use frontend-image-to-code.`

**项目级 `AGENTS.md`** 放在项目根目录，描述项目结构、技术栈、开发规范等。在该项目中会覆盖全局设置。

---

## 致谢

本仓库的部分安装指南参考了 [orionpax1997/kickstart.pi](https://github.com/orionpax1997/kickstart.pi) 的结构，并按本配置的组件与全局安装方式做了调整。
