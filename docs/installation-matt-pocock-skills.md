# Install mattpocock/skills

> [mattpocock/skills](https://github.com/mattpocock/skills) is Matt Pocock's collection of engineering skills. It covers design grilling, TDD, debugging, code review, architecture surveys, issue triage, and more. Most skills load automatically when a task matches; some are also available as `/skill:<name>` commands.

## Install globally

```bash
npx skills@latest add mattpocock/skills --agent pi --global -y
```

The installer downloads the repo, writes every skill to `~/.agents/skills/<name>/`, and creates symlinks under `~/.pi/agent/skills/<name>/`. One install is enough for all projects.

### Install only some skills

```bash
npx skills@latest add mattpocock/skills --agent pi --global --skill \
  setup-matt-pocock-skills \
  grill-me \
  tdd \
  diagnosing-bugs \
  triage -y
```

### Common flags

| Flag | Purpose |
| --- | --- |
| `--global` | Install at the user level (`~/.agents/skills/` + `~/.pi/agent/skills/` links) instead of the current project |
| `--agent pi` | Target pi (writes the `~/.pi/agent/skills/` links). Omit to install for every supported agent. |
| `--skill <name>` | Install one specific skill. Repeat to install several. Use `'*'` for all. |
| `-y, --yes` | Skip prompts (non-interactive). |
| `--list` | List the skills in the repo without installing. |

```bash
# Browse what's available before committing
npx skills@latest add mattpocock/skills --list

# Install everything
npx skills@latest add mattpocock/skills --agent pi --global --skill '*' -y
```

## One-time setup per repo

After installing, run `/skill:setup-matt-pocock-skills` once in each project. It asks three questions and writes configuration into that repo:

1. **Which issue tracker?** GitHub, GitLab, Linear, or local files
2. **What triage labels do you use?** (the `/triage` skill needs them)
3. **Where do domain docs (`CONTEXT.md`, ADRs) live?**

Several other skills read this configuration (e.g. `/triage`, `/to-spec`, `/to-tickets`, `/grill-with-docs`), so the wizard is effectively a prerequisite — choose it during install.

## What you get

Skills fall into two groups: **user-invoked** (they only run when you type them, like `/skill:grill-me`; they orchestrate a workflow) and **model-invoked** (they can be loaded automatically when the task fits; they carry the reusable discipline).

Highlights:

| Skill | Trigger | What it does |
| --- | --- | --- |
| `grill-me` | `/skill:grill-me` | Interviews you hard to sharpen a plan or design before writing code |
| `grill-with-docs` | `/skill:grill-with-docs` | The same grilling, plus it writes `CONTEXT.md` and ADRs as decisions land |
| `tdd` | auto + `/skill:tdd` | Red-green-refactor loop for features and fixes |
| `diagnosing-bugs` | auto + `/skill:diagnosing-bugs` | A disciplined, phase-gated loop for tracking down bugs |
| `triage` | auto + `/skill:triage` | Moves issues and external PRs through a triage state machine |
| `code-review` | auto + `/skill:code-review` | Reviews changes since a fixed point against standards and spec |
| `domain-modeling` | auto + `/skill:domain-modeling` | Nails down terminology and the project's ubiquitous language |
| `research` | auto + `/skill:research` | Investigates a question against primary sources and writes up the findings as Markdown |
| `resolving-merge-conflicts` | auto + `/skill:resolving-merge-conflicts` | Walks you through an in-progress merge or rebase conflict |
| `wizard` | auto + `/skill:wizard` | Generates an interactive bash wizard for steps only a human can do |

The full list is at [skills.sh/mattpocock/skills](https://skills.sh/mattpocock/skills).

## Updating

```bash
npx skills@latest update -g -y
```

## Scope

A global install writes:

- `~/.agents/skills/<name>/` — the actual skill content
- `~/.pi/agent/skills/<name>` — symlinks so pi discovers each skill in every project

It leaves your model settings, theme, and pi configuration alone. Running `/skill:setup-matt-pocock-skills` in a project additionally writes project-local config under `.agents/`.

Uninstall:

```bash
npx skills@latest remove <name>
```
