# Install pi-permission-system for pi

[@gotgenes/pi-permission-system](https://pi.dev/packages/@gotgenes/pi-permission-system) is a permission enforcement extension: it gates tool, bash, MCP, and skill calls against a single policy file. Every decision is one of `allow` / `deny` / `ask`, and the policy is layered — `path` → `external_directory` → per-tool patterns → `bash` patterns — with a UI confirmation dialog for anything that isn't pre-approved.

> **Sub-agents inherit the same policy.** This setup runs [`pi-subagents`](https://pi.dev/packages/pi-subagents), whose children execute as non-UI sessions. The permission system forwards `ask` prompts from those sessions up to the parent's dialog, so a child's tool calls are gated by the same rules as the main agent — no separate configuration. Verify it once with step 4 below.

## Install

```bash
pi install npm:@gotgenes/pi-permission-system
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: the policy file applies to every session. Project-level overrides live in `<cwd>/.pi/extensions/pi-permission-system/config.json` and can only tighten the global policy — they cannot relax a global `deny`.

## Configure

Create the global policy file at `~/.pi/agent/extensions/pi-permission-system/config.json`:

```jsonc
{
  "permission": {
    "*": "allow",
    "path": {
      "*": "allow",
      "*.env": "deny",
      "*.env.*": "deny",
      "*.env.example": "allow"
    },
    "bash": {
      "*": "ask",
      "rm -rf *": "deny",
      "sudo *": "ask"
    },
    "external_directory": "ask"
  }
}
```

Then restart pi, or run `/reload` in the current session.

### What each surface does

| Surface | Purpose |
| --- | --- |
| `path` | Cross-cutting gate for **all** file access (tools + bash + MCP + extensions). The right place for `.env`, `~/.ssh/*`. |
| `external_directory` | CWD-boundary gate — prompts before file tools or bash reach outside the working tree. Accepts a pattern map, so specific outside-CWD directories (e.g. `~/.cargo/registry`) can be `allow`-listed. |
| `bash` | Command pattern matching with `*` wildcards. The last matching rule wins. |
| `*` | Coarse fallback for everything not covered above. |

The layers compose **most-restrictive-wins**: a `path: "deny"` cannot be loosened by a per-tool `allow`, and an `external_directory: "ask"` cannot be loosened by `path: "allow"`. The package also exposes directional variants — `path_read` / `path_write` and `external_directory_read` / `external_directory_write` — for cases where reading somewhere should be allowed but writing should not.

### Permission states

| State | Behavior |
| --- | --- |
| `allow` | Permits the action silently. |
| `deny` | Blocks the action and returns an error message to the LLM. |
| `ask` | Pops a UI dialog. You can approve once, or approve a pattern for the rest of the session. |

## Verify

In a fresh `pi` session:

1. Ask the agent to `cat ~/.ssh/id_ed25519` — it should be blocked by the `path` deny.
2. Ask the agent to read `../some-other-project/README.md` — it should raise an `external_directory: ask` dialog, because the path leaves the cwd.
3. Ask the agent to run `rm -rf node_modules` — it should be blocked by `bash: "rm -rf *": "deny"`.
4. Ask the agent to delegate `rm -rf node_modules` to a sub-agent — the same prompt should appear in the parent session, confirming that sub-agent asks are forwarded.

If any of these pass silently, the config isn't being read: double-check the path and that the JSON parses (`jq . ~/.pi/agent/extensions/pi-permission-system/config.json`).

## Customize

- **Project overrides** — drop a `config.json` into `<cwd>/.pi/extensions/pi-permission-system/`. Project config loads only when the directory is trusted, so an untrusted repository cannot loosen your global policy.
- **Per-agent overrides** — add frontmatter to an agent definition (`~/.pi/agent/agents/<name>.md`) for role-specific policies, e.g. stricter bash rules for an `explore` agent.
- **Patterns** — `*` is the only wildcard. Approving a similar prompt once makes the dialog offer a generated rule you can reuse.

## Uninstall

```bash
pi remove npm:@gotgenes/pi-permission-system
```

This removes the entry from `~/.pi/agent/settings.json`. The policy file and review logs under `~/.pi/agent/extensions/pi-permission-system/` are left in place — delete them manually for a fully fresh start:

```bash
rm -rf ~/.pi/agent/extensions/pi-permission-system
```

## Scope

- Adds the permission gate, hides disallowed tools before the agent starts, and forwards `ask` prompts from non-UI child sessions (sub-agents) to the parent UI.
- Fails closed: an internal gate error blocks the tool rather than letting it through.
- Does not change the main agent's behavior, system prompt, default tool list, model selection, or session lifecycle — remove the gate and pi behaves exactly as before.
