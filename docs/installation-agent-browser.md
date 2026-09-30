# Install agent-browser

> [agent-browser](https://github.com/vercel-labs/agent-browser) lets the pi agent drive a real browser over CDP (Chrome DevTools Protocol), which is more token-efficient than the usual headless-browser approach.

## Prerequisites

```bash
which agent-browser || npm i -g agent-browser && agent-browser install
```

`agent-browser install` downloads the Chromium binary it needs.

## Install the skill globally

```bash
npx skills@latest add https://github.com/vercel-labs/agent-browser \
  --skill agent-browser --agent pi --global -y
```

This writes the skill to `~/.agents/skills/agent-browser/` and symlinks it into `~/.pi/agent/skills/agent-browser/`, so pi discovers it in every project.

## Usage

In a pi session, the agent can drive the browser through the `agent-browser` skill — open pages, click elements, fill forms, take screenshots, and read console / network output. Ask the agent to do something on a real browser and it invokes the skill.

The installed skill is a small discovery stub. Before running any command it loads the current workflow content straight from the CLI, so the instructions never go stale:

```bash
agent-browser skills get core      # workflows, common patterns, troubleshooting
agent-browser skills get core --full   # full command reference and templates
agent-browser skills list          # everything bundled with the installed version
```

Specialized content works the same way — for example `agent-browser skills get electron`, `agent-browser skills get slack`, or `agent-browser skills get dogfood`.

## Why this setup installs it globally

- The skill is a stub that fetches instructions on demand, so the always-on context cost is one short description.
- The global `agent-browser` binary is already installed and warmed up for every project.
- One global copy tracks one CLI version — no per-repo `.pi/skills/` copies to keep in sync.

## Updating

```bash
npm i -g agent-browser@latest && agent-browser install
npx skills@latest update -g -y
```

## Uninstall

```bash
npx skills@latest remove agent-browser
npm uninstall -g agent-browser
```

## Scope

- Requires the `agent-browser` CLI on `PATH` plus the Chromium binary it manages.
- Browser sessions, authentication state, and video recordings are managed by the CLI, not by pi.
- The stub itself is short — the detailed instructions are pulled from the CLI only when the skill runs.
