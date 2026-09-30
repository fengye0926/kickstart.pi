# Install pi-subagents for pi

[@tintinweb/pi-subagents](https://pi.dev/packages/@tintinweb/pi-subagents) adds Claude Code-style autonomous sub-agents to pi. Each spawned agent runs in its own isolated session with its own tools, system prompt, model, and thinking level; you can run them in the foreground or background, steer them while they work, and declare custom agent types in `.pi/agents/*.md` (project) or globally.

## Install

```bash
pi install npm:@tintinweb/pi-subagents
```

The extension is recorded in your global pi settings (`~/.pi/agent/settings.json`) and picked up on startup.

> Install at the **global** level: the sub-agent tools (`Agent`, `get_subagent_result`, `steer_subagent`) are session-level capabilities — one install covers every project. Per-project agent definitions still work through `.pi/agents/*.md`.

## Verify

In a fresh `pi` session:

- the `Agent` tool is available to the model whenever it decides to delegate work;
- `/agents` opens the FleetView — a navigable list of the main session plus any running sub-agents.

## Activate

Restart pi, or run `/reload` in the current session.

## Configure

### The `/agents` command

FleetView is where you inspect, navigate, and steer running sub-agents:

- `↓` / `←` on an empty prompt — jump into FleetView
- `↑` / `↓` — move the selection
- `Enter` — open the selected agent's live, auto-updating conversation
- `Esc` — go back to the main session
- `Enter` on a running agent — open an inline composer to steer it; `Enter` sends, `Esc` or an empty submit cancels
- `x`, then `x` again to confirm — stop a running agent

Completed agents stay in the list briefly before disappearing, and the conversation viewer remains open so you can read the final output.

### Widget visibility

`/agents → Settings → Widget`:

- `all` — show foreground and background agents
- `background` (default) — hide foreground runs, which already render inline as `Agent` tool results
- `off` — turn the persistent widget off

### Custom agent types

Declare agents in `.pi/agents/*.md` (project) or `~/.pi/agent/agents/*.md` (global) with YAML frontmatter:

```markdown
---
name: reviewer
description: Reviews code for correctness and style
model: sonnet
thinking: medium
---

You are a senior engineer reviewing code for correctness, style, and edge cases.
```

Frontmatter keys include `name`, `description`, `model`, `thinking`, and `tools` (limit the agent to a subset of tools). Custom types are auto-discovered and offered to the main agent next to the built-in ones.

## Uninstall

```bash
pi remove npm:@tintinweb/pi-subagents
```

This removes the entry from `~/.pi/agent/settings.json`; custom agent definitions under `.pi/agents/` and `~/.pi/agent/agents/` are left alone.

## Scope

pi-subagents adds sub-agent tools and the FleetView UI. It does not change the main agent's behavior, system prompt, or default tool list.
