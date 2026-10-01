# Install pi-subagents for pi

[pi-subagents](https://pi.dev/packages/pi-subagents) lets Pi delegate work to focused child agents — code review, scouting, implementation, parallel audits, saved workflows, background jobs. It ships ready-to-use agents and a `subagent` tool, and Pi decides when to call it, so you can ask in plain language instead of configuring anything first.

## Install

```bash
pi install npm:pi-subagents
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup, and the package also registers its own prompt shortcuts (`/parallel-review`, `/review-loop`, `/council`, …) and skills.

> Install at the **global** level: delegation is a session-level capability — one install covers every project. Per-project agent definitions can still be added under `.pi/agents/`.

## Verify

In a fresh `pi` session:

- Ask in plain language, e.g. *"Use reviewer to review this diff."* — Pi calls the `subagent` tool and the child's progress streams in the conversation.
- `/subagents-fleet` opens the live inspector: browse children, read transcripts, steer a running child, or stop a run.
- `/subagents-doctor` checks that subagents are configured correctly.
- `/subagents-guide` prints help for the installed version; `/subagents-guide workflows` covers orchestration patterns.

## Activate

Restart pi, or run `/reload` in the current session.

## Built-in agents

| Agent | Use it for |
| --- | --- |
| `scout` | Fast local codebase recon: relevant files, entry points, data flow, risks |
| `researcher` | Web and docs research with sources and a concise brief |
| `evidence-auditor` | Checks whether important research claims are supported by their sources |
| `worker` | Implementation work: edits files, validates, escalates unapproved decisions |
| `reviewer` | Code review and small fixes against the task, tests, edge cases, and simplicity |
| `oracle` | A second opinion before acting; challenges assumptions without editing |
| `delegate` | A lightweight general delegate that behaves close to the parent session |

A practical default loop for implementation work is `clarify → scout → worker → fresh reviewers → worker`.

## Background runs

Foreground runs stream progress in the conversation; background runs keep working after control returns to you. While work is active, a persistent FleetView sits below the editor, and `/subagents-fleet` opens the full inspector. You can also just ask: *"Show me the current async runs."*

## Configure

Optional. Config lives at `~/.pi/agent/extensions/subagent/config.json`; without the file the defaults apply. Keys cover model and thinking defaults, concurrency, per-run spawn limits (`maxSubagentSpawnsPerRun`, default 64), and watchdog options. The package's `docs/configuration.md` lists every key and environment variable.

Custom agents are markdown files with frontmatter, discovered from `.pi/agents/` (project) and `~/.pi/agent/agents/` (global); they can override the built-ins.

## Uninstall

```bash
pi remove npm:pi-subagents
```

## Scope

- Adds the `subagent` tool, the packaged prompts and skills, and the fleet UI. It does not otherwise change the main agent's behavior.
- Agents that do web research need `pi-web-access` available to the child session — see the package's agents documentation.
- Source and full reference: [nicobailon/pi-subagents](https://github.com/nicobailon/pi-subagents).
