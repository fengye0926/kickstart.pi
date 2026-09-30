# Install rtk for pi

[rtk](https://github.com/rtk-ai/rtk) rewrites verbose shell commands (`git status`, `pnpm list`, `vitest`, `cargo test`, …) into a compact token-saving form. Every pi session is intercepted and rewritten automatically — the workflow stays the same, the output gets shorter.

## Install

```bash
rtk init --agent pi --global
```

This writes `~/.pi/agent/extensions/rtk.ts`, which pi discovers on startup.

> Install at the **global** level: the extension is most useful when every project benefits from token-saved bash output, so one install covers every project.

## Verify

```bash
rtk init --show
```

Then open a fresh `pi` session and run a chatty command such as `git status` — pi should receive the shortened form instead of the raw output.

## Activate

Restart pi, or run `/reload` in the current session.

## Uninstall

```bash
rtk init --uninstall --agent pi --global
```

Only the installed pi extension file is removed; other files under `~/.pi/agent/extensions/` are left alone.

## Scope

The pi integration currently rewrites the `bash` tool call only. `read` / `grep` / `find` / `ls`, `write`, and `edit` are not touched.
