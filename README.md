# kickstart.pi

A well-commented, ready-to-use starting point for [Pi](https://github.com/earendil-works/pi-mono) that helps you actually understand what's happening — built from a real day-to-day setup.

> Only the components this setup actually runs are documented here — one short installation guide each, all installed at the **global** level.

[English](README.md) | [简体中文](README.zh-cn.md)

---

## Table of contents

- [Philosophy](#philosophy)
- [Quick start](#quick-start)
- [Project structure](#project-structure)
- [How to make it yours](#how-to-make-it-yours)
- [What this is NOT](#what-this-is-not)
- [Built-in](#built-in)
- [Beautification](#beautification)
- [Saving tokens](#saving-tokens)
- [Agentic Workflow](#agentic-workflow)
- [Session interaction](#session-interaction)
- [Code intelligence](#code-intelligence)
- [Additional features](#additional-features)
- [AGENTS.md](#agentsmd)
- [Credits](#credits)

---

## Philosophy

Most "AI config starter" repos give you a finished product. You get power, but you don't understand what's happening or why.

**kickstart.pi does the opposite:**

- Every guide is short and focused on one component
- Every decision is explained, not just shown
- It documents only the components this setup actually uses — no catalog of things you don't run
- Everything installs at the **global** level (`~/.pi/agent/`), so one install covers every project
- It's a starting point, not a framework — add your own settings, delete what's unused

---

## Quick start

**Let pi install it for you** (recommended)

Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation.md
```

**Manual installation**

> ⚠️ If `~/.pi/agent/` already has config, back it up first. `cp -an` below only adds files that don't exist yet.

```bash
mkdir -p ~/.pi/agent
repo_dir="$(mktemp -d)"
git clone https://github.com/fengye0926/kickstart.pi "$repo_dir"
cp -an "$repo_dir"/. ~/.pi/agent/
rm -rf "$repo_dir"
```

Then start pi in any project:

```bash
cd /path/to/project
pi
```

---

## Project structure

```
kickstart.pi/
│
├── LICENSE
├── README.md            ← you are here
├── README.zh-cn.md      ← Chinese version
├── skills/
│   └── frontend-image-to-code/
│       └── SKILL.md     ← bundled skill: design image → working frontend code
└── docs/
    ├── installation.md                       ← MCP setup: native MCP + exa / context7 / searchcode
    ├── installation-open-tui.md              ← pi-open-tui TUI polish (global)
    ├── installation-rtk.md                   ← token-saving bash rewriter (global)
    ├── installation-matt-pocock-skills.md    ← engineering skills (global)
    ├── installation-subagents.md             ← pi-subagents (global)
    ├── installation-permission-system.md     ← permission gates for tools, bash, MCP, skills (global)
    ├── installation-rpiv-todo.md             ← persistent todo panel (global)
    ├── installation-ask-user-question.md     ← structured question dialogs (global)
    ├── installation-pi-lsp.md                ← targeted LSP diagnostics and fixes (global)
    ├── installation-agent-browser.md         ← browser automation skill (global)
    └── installation-frontend-image-to-code.md ← the bundled skill (global)
```

That's it. No `settings.json`, no agents, no bundled extensions — just the guides under [`docs/`](docs) and one example skill. The one required piece of setup is the three MCP servers configured in Step 2 of [`docs/installation.md`](docs/installation.md) (`context7`, `searchcode`, `exa`) — Pi speaks MCP natively, so no adapter package is needed.

---

## How to make it yours

1. **Read `README.md` from top to bottom** — understand the philosophy and what's available.
2. **Decide what goes in your `~/.pi/agent/settings.json`** — this repo ships no config by design. Set your preferred provider, model, and theme; pi walks you through this on first launch.
3. **Configure the three MCP servers** — Pi has native MCP support; follow Step 2 and Step 3 of [`docs/installation.md`](docs/installation.md). They're what the global `AGENTS.md` queries.
4. **Install the global add-ons you want** — [pi-open-tui](docs/installation-open-tui.md), [rtk](docs/installation-rtk.md), [mattpocock/skills](docs/installation-matt-pocock-skills.md), [pi-subagents](docs/installation-subagents.md), [pi-permission-system](docs/installation-permission-system.md), [rpiv-todo](docs/installation-rpiv-todo.md), [ask-user-question](docs/installation-ask-user-question.md), [pi-lsp](docs/installation-pi-lsp.md), [agent-browser](docs/installation-agent-browser.md), and the bundled [frontend-image-to-code](docs/installation-frontend-image-to-code.md). Use `pi list` to see what's installed and `pi config` to enable or disable resources.
5. **Customize the global `AGENTS.md`** — MCP usage hints, language preference, working style, skill routing.
6. **Add project-level `AGENTS.md`** in repos that need it — project structure, tech stack, coding conventions.
7. **Create your own prompt templates** in `.pi/prompts/` for repetitive workflows (`/your-command`).

Delete anything you don't use. It's a starting point, not a framework.

---

## What this is NOT

- Not a multi-agent orchestration system
- Not a production-ready AI pipeline
- Not something you use without reading

---

## Built-in

The install flow in [`docs/installation.md`](docs/installation.md) sets up everything this repo needs to be useful out of the box:

> **MCP is built into Pi.** Pi 0.99+ connects to MCP servers natively, so no adapter package is required. This setup keeps three servers in `~/.pi/agent/mcp.json` so the agent can reach the wider tool ecosystem.

**Three MCP servers** — configured exactly as in Step 2 of the installation guide:

- **context7** — library and framework documentation (direct)
- **searchcode** — public repository code search (deferred)
- **exa** — web search (direct)

`pi mcp list` checks the connections; `/mcp` in a session shows the same view and lets you toggle servers or change exposure.

**No `settings.json` shipped** — pi walks you through provider / model / theme selection on first launch. If you already have a backup, restore it with `cp ~/.pi/agent.bak/settings.json ~/.pi/agent/`.

---

## Beautification

### pi-open-tui

[pi-open-tui](https://pi.dev/packages/pi-open-tui) is a polished TUI styling extension for pi — an animated header, a two-line [Starship](https://starship.rs/)-inspired footer (cwd, git branch & status, runtime version, context bar, model, tokens, cost), a rounded editor, and turn telemetry (TPS / TTFT / stalls / cost) shown after each run. Everything is configurable from inside pi with `/open-tui`.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-open-tui.md
```

Then `/open-tui` to configure the header, footer segments, icon mode, and telemetry.

---

## Saving tokens

### rtk

[rtk](https://github.com/rtk-ai/rtk) transparently rewrites verbose shell commands (`git status`, `pnpm list`, `vitest`, `cargo test`, …) into compact token-saving form. Each pi session is intercepted and rewritten automatically — same workflow, fewer tokens.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-rtk.md
```

`rtk init --agent pi --global` creates `~/.pi/agent/extensions/rtk.ts`, which pi auto-discovers on startup. The extension fails open: if `rtk` is missing, outdated, or a rewrite times out, the original command runs unchanged.

---

## Agentic Workflow

### mattpocock/skills

[mattpocock/skills](https://github.com/mattpocock/skills) is a curated set of engineering skills by Matt Pocock — grilling interviews, TDD, diagnosing bugs, code review, domain modeling, research, and more. Skills auto-load per task; several also register as `/skill:<name>` commands.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-matt-pocock-skills.md
```

Skills land in `~/.agents/skills/` and are symlinked into `~/.pi/agent/skills/`. Run `/skill:setup-matt-pocock-skills` once per repo to pick the issue tracker, triage labels, and where `CONTEXT.md` / ADRs live.

---

## Session interaction

### rpiv-todo

[@juicesharp/rpiv-todo](https://www.npmjs.com/package/@juicesharp/rpiv-todo) gives the model a task list you can see: a `todo` tool, a `/todos` command, and a live panel above the editor showing what is done, in progress, and queued. The list is rebuilt from the conversation, so it survives `/reload` and compaction.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-rpiv-todo.md
```

Then `/todos` to confirm the panel is loaded.

### ask-user-question

[@juicesharp/rpiv-ask-user-question](https://www.npmjs.com/package/@juicesharp/rpiv-ask-user-question) gives the model one tool — `ask_user_question` — that opens a terminal dialog of up to four questions with authored options instead of guessing. You can answer in your own words, attach notes, and compare previews side by side.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-ask-user-question.md
```

Then hand the model a task with a real decision buried in it and pick an answer instead of undoing an assumption.

---

## Code intelligence

### pi-lsp

[@narumitw/pi-lsp](https://www.npmjs.com/package/@narumitw/pi-lsp) gives pi targeted LSP diagnostics (`lsp_diagnostics`) and source fixes (`lsp_fix`) during an edit. Language servers are configured as command + file-extension routes instead of hard-coded language families, start only for matching tool calls, and shut down afterwards.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-pi-lsp.md
```

Then `/lsp` to check which configured server commands are available on `PATH`.

---

## Additional features

### subagents

[pi-subagents](https://pi.dev/packages/pi-subagents) lets Pi delegate work to focused child agents — code review, scouting, implementation, parallel audits, saved workflows, background jobs. It ships ready-to-use agents (`scout`, `researcher`, `worker`, `reviewer`, `oracle`, `delegate`) and a `subagent` tool, so you can ask in plain language — *"Use reviewer to review this diff."* — instead of configuring anything first.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-subagents.md
```

Then `/subagents-fleet` to inspect, steer, or stop running children, and `/subagents-doctor` to check the setup.

### pi-permission-system

[@gotgenes/pi-permission-system](https://pi.dev/packages/@gotgenes/pi-permission-system) puts a deterministic permission gate between the agent and every tool, bash, MCP, and skill call. Three states (`allow` / `deny` / `ask`) and four layered surfaces (`path` → `external_directory` → per-tool patterns → `bash` patterns) cover most of what a coding agent can do; anything not pre-approved raises a UI dialog. `ask` prompts from sub-agent sessions are forwarded to the parent, so this setup's [pi-subagents](#subagents) operations run under the same policy.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-permission-system.md
```

Then edit `~/.pi/agent/extensions/pi-permission-system/config.json` to define your policy — start from the safe defaults in the guide (deny `.env`, `ask` bash, `ask` outside-cwd) and tighten from there.

### agent-browser

[agent-browser](https://github.com/vercel-labs/agent-browser) lets the pi agent control a browser via CDP (Chrome DevTools Protocol) — more token-efficient than traditional headless browser approaches. It installs as a global skill whose stub loads the current workflow content from the `agent-browser` CLI on demand, so it never goes stale.

Install at the **global** level. Paste this into pi:

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/fengye0926/kickstart.pi/refs/heads/main/docs/installation-agent-browser.md
```

### frontend-image-to-code

This repo bundles one skill: [`frontend-image-to-code`](skills/frontend-image-to-code/SKILL.md). Give the agent a screenshot, mockup, or Figma export, and it derives an implementable spec, maps it onto the existing project stack, implements it, and compares a rendered result against the reference — without inventing a new visual direction. The skill text is written in Chinese.

Install at the **global** level (from a clone of this repo):

```bash
mkdir -p ~/.pi/agent/skills
cp -R skills/frontend-image-to-code ~/.pi/agent/skills/
```

Details and verification steps: [`docs/installation-frontend-image-to-code.md`](docs/installation-frontend-image-to-code.md).

---

## AGENTS.md

AGENTS.md is the global instruction file pi loads into every session. Keep it short — only include what truly applies everywhere.

pi also auto-discovers `AGENTS.md` / `CLAUDE.md` walking up from the cwd, so a project-level copy in the project root overrides the global one for that project.

**Suggested content for the global file:**

- **MCP usage hints** — e.g. `Use exa for web search (current information, news, facts).`
- **Language preference** — e.g. `Reply in Chinese.`
- **Personal coding preferences** — e.g. `Never use any type. Prefer explicit types.`
- **Working style** — e.g. `Keep responses concise. No need to explain obvious steps.`
- **Skill routing** — e.g. `For screenshot → frontend code requests, use frontend-image-to-code.`

**Project-level `AGENTS.md`** goes in the project root, describing project structure, tech stack, development standards, etc. It overrides the global for that project.

---

## Credits

Several installation guides in this repo follow the structure of [orionpax1997/kickstart.pi](https://github.com/orionpax1997/kickstart.pi), adapted to this setup's components and global installation.
