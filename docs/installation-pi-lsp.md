# Install pi-lsp for pi

[@narumitw/pi-lsp](https://www.npmjs.com/package/@narumitw/pi-lsp) gives pi targeted Language Server Protocol diagnostics and source fixes during an edit. Language servers are configured by command and file extension instead of being hard-coded to language families, so one configurable diagnostics interface covers a multi-language repository.

## Install

```bash
pi install npm:@narumitw/pi-lsp
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

To try it without installing permanently:

```bash
pi -e npm:@narumitw/pi-lsp
```

> Install at the **global** level: diagnostics are a session-level capability — one install covers every project. Language-server requirements are installed separately.

## Verify

1. Install at least one language server from the built-in catalog on `PATH` (pi-lsp never downloads servers itself).
2. In a fresh `pi` session, run `/lsp` to see configured commands and their availability.
3. Ask the model to run `lsp_diagnostics` on a file you are editing, and optionally `lsp_fix` for a server-supported source action.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Configure

Without a settings file, pi-lsp uses its built-in server catalog. Add a config only when you need custom routes.

Precedence:

1. `<workspace>/.pi/pi-lsp.json` — only when Pi trusts that project
2. `~/.pi/agent/pi-lsp.json`
3. Built-in catalog

A custom configuration **replaces** the entire server map rather than merging with the defaults. For example, this file selects only Ruff:

```json
{
  "ruff": {
    "command": ["ruff", "server"],
    "extensions": [".py", ".pyi"]
  }
}
```

Multiple servers can be routed for the same extension when complementary diagnostics are useful; pass `server` explicitly on a call when more than one matches.

## Tools

| Tool | Purpose |
| --- | --- |
| `lsp_diagnostics` | Run diagnostics through configured servers for files or directories |
| `lsp_fix` | Apply source fixes or import organization for a file, preview or write |

`lsp_format` is no longer provided — use project formatters or shell commands for formatting workflows.

## Uninstall

```bash
pi remove npm:@narumitw/pi-lsp
```

## Scope

- Record the repository's authoritative format / lint / typecheck / build / test commands in `AGENTS.md`. Use pi-lsp for intermediate feedback, then run those commands before declaring a task complete.
- Language servers start and stop per tool call — pi-lsp does not keep an editor-like incremental session.
- The tools cover diagnostics and source actions, not symbol navigation, references, or semantic rename.
- Overlapping calls share one activity status; avoid concurrent edits to the same file because `lsp_fix` writes do not participate in Pi's shared file-mutation queue.
- Configured commands run with your user permissions and inherit Pi's environment; review every command, argument, and `env` override before use.
- Source and full reference: [narumitw/pi-lsp](https://github.com/narumitw/pi-lsp).
