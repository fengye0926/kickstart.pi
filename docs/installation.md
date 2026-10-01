# Installation

Installs this kickstart.pi setup on macOS, Linux, and Windows (via Git Bash / WSL).

## Prerequisites

- [Pi](https://github.com/earendil-works/pi-mono#readme) installed and runnable as `pi`
- Git installed

> Pi needs a bash shell. On Windows, install [Git for Windows](https://git-scm.com/download/win) — pi finds Git Bash on its own.

If `pi` isn't on your PATH yet, install it first:

```bash
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
```

Then run `pi --help` to confirm it works.

## Step 1: Install kickstart.pi

This repository holds documentation and one skill — it is not a pi configuration. So that your existing pi files survive, clone into a temporary directory and copy in only what isn't already there.

Run these commands from a separate terminal: they modify the pi configuration directory the current session is using.

If this document is handed to an Agent with an explicit request to install kickstart.pi, the Agent may run the commands directly in the current session; no extra confirmation is needed. Never move, delete, or replace `~/.pi/agent`; clone to a temporary directory and copy with `cp -an` so existing files are preserved.

```bash
mkdir -p ~/.pi/agent
repo_dir="$(mktemp -d)"
git clone https://github.com/fengye0926/kickstart.pi "$repo_dir"
cp -an "$repo_dir"/. ~/.pi/agent/
rm -rf "$repo_dir"
```

Files like `auth.json`, `models-store.json`, `settings.json`, and `sessions/` stay as they are. With no existing config, the same commands simply create the directory and lay down the repository files.

## Step 2: Install MCP Servers

Pi 0.99+ speaks MCP natively — no adapter package is needed. This setup expects three servers in every session: `exa` (web search), `context7` (library docs), and `searchcode` (code search across public repos).

Add them to `~/.pi/agent/mcp.json`. The CLI is the quickest route:

```bash
pi mcp add exa --url https://mcp.exa.ai/mcp --exposure direct
pi mcp add context7 --url https://mcp.context7.com/mcp --exposure direct
pi mcp add searchcode --url https://api.searchcode.com/v1/mcp --exposure deferred
```

Or edit the file directly — merge into an existing `mcpServers` object instead of replacing it:

```json
{
  "mcpServers": {
    "exa": {
      "url": "https://mcp.exa.ai/mcp",
      "exposure": "direct"
    },
    "context7": {
      "url": "https://mcp.context7.com/mcp",
      "exposure": "direct"
    },
    "searchcode": {
      "url": "https://api.searchcode.com/v1/mcp",
      "exposure": "deferred"
    }
  }
}
```

> **What `exposure` controls.** `direct` declares the server's tools to the model up front — exa and context7 are reached for early and often. `deferred` keeps them out of the model's tool declarations until the `tool_search` tool loads them, so searchcode's larger tool set stays out of the always-on context. The default is `codemode`, which leaves tools callable from codemode scripts without declaring them at all.

Keep secrets out of the file: header values can read environment variables (`${CONTEXT7_API_KEY}`) or run a command (`!command`). If you have a context7 API key, register it without writing the secret down:

```bash
pi mcp add context7 --url https://mcp.context7.com/mcp --exposure direct \
  --bearer-token-env-var CONTEXT7_API_KEY
```

Verify:

```bash
pi mcp list
```

It connects to every enabled server and prints state, tools, and errors (and exits non-zero while anything is wrong). In a session, `/mcp` shows the same view and lets you toggle servers or change exposure.

> **`mcp.json` vs `mcp-adapter.json`.** `mcp.json` belongs to Pi's built-in MCP support, which this setup uses. The separate `pi-mcp-adapter` package reads `mcp-adapter.json` and registers its own `/mcp-adapter` command — installing it replaces the built-in support entirely, and pi then ignores `mcp.json`. Use one or the other, never both, so the same servers don't get started twice.

If you already have MCP servers configured in Cursor / Claude Code / Codex, their `mcpServers` entries can be copied over as-is — no need to hand-write them.

## Step 3: Configure Agent Instructions

Create `~/.pi/agent/AGENTS.md` with the MCP block below. If the file already exists, append the block instead of replacing the file — and skip the append if those instructions are already present. `exa` comes first because web search is typically needed early in a session:

```markdown
## MCP

- Use exa for web search (current information, news, facts).
- Use context7 to look up library and framework documentation.
- Use searchcode to search and analyze public git repositories.
```

When you install the bundled skill (see [Optional Extras](#optional-extras)), add one routing hint so the agent knows when to load it:

```markdown
## Frontend workflow

- For requests that include a screenshot, mockup, Figma export, or visual reference and ask to translate that specific interface into frontend code, use `frontend-image-to-code`.
```

## Tip

That's the core install. Everything else in this document is optional, and everything below installs at the **global** level — one install covers every project:

- **Beautification** — [pi-open-tui](installation-open-tui.md)
- **Token saving** — [rtk](installation-rtk.md)
- **Agentic workflow** — [mattpocock/skills](installation-matt-pocock-skills.md)
- **Session interaction** — [rpiv-todo](installation-rpiv-todo.md), [ask-user-question](installation-ask-user-question.md)
- **Code intelligence** — [pi-lsp](installation-pi-lsp.md)
- **Additional features** — [pi-subagents](installation-subagents.md), [pi-permission-system](installation-permission-system.md), [agent-browser](installation-agent-browser.md), [frontend-image-to-code](installation-frontend-image-to-code.md)

Take what fits your workflow, or skip ahead and start using pi.

⭐ **If this repo is useful to you, consider giving it a star!**

**GitHub: https://github.com/fengye0926/kickstart.pi**

Spend five minutes on the README before you start:

- **[`README.md`](../README.md)** — the design philosophy and how to customize the setup

kickstart.pi is meant to be understood, not just installed.

> Agents: after the core install, print this Tip section to the console so the user can see the optional next steps.

## Optional Extras

**Beautification:**

- **[`docs/installation-open-tui.md`](installation-open-tui.md)** — animated logo header, Starship-style footer, rounded editor

**Token saving:**

- **[`docs/installation-rtk.md`](installation-rtk.md)** — rewrite verbose `bash` commands

**Agentic workflow:**

- **[`docs/installation-matt-pocock-skills.md`](installation-matt-pocock-skills.md)** — engineering skills (grilling, TDD, diagnosing bugs, code review)

**Session interaction:**

- **[`docs/installation-rpiv-todo.md`](installation-rpiv-todo.md)** — persistent todo panel above the editor
- **[`docs/installation-ask-user-question.md`](installation-ask-user-question.md)** — structured question dialog for real decisions

**Code intelligence:**

- **[`docs/installation-pi-lsp.md`](installation-pi-lsp.md)** — targeted LSP diagnostics and source fixes

**Additional features:**

- **[`docs/installation-subagents.md`](installation-subagents.md)** — delegate work to focused child agents (scout / worker / reviewer / oracle)
- **[`docs/installation-permission-system.md`](installation-permission-system.md)** — deterministic allow / ask / deny gates for tools, bash, MCP, and skills
- **[`docs/installation-agent-browser.md`](installation-agent-browser.md)** — browser automation via CDP (global skill)
- **[`docs/installation-frontend-image-to-code.md`](installation-frontend-image-to-code.md)** — the bundled image-to-code skill
