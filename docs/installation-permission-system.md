# Install pi-permission-system for pi

[@gotgenes/pi-permission-system](https://pi.dev/packages/@gotgenes/pi-permission-system) is a permission enforcement extension: it gates tool, bash, MCP, and skill calls against a single policy file. Every decision is one of `allow` / `deny` / `ask`, and the policy is layered — `path` → `external_directory` → per-tool patterns → `bash` patterns — with a UI confirmation dialog for anything that isn't pre-approved.

> **Sub-agents: background yes, foreground no.** Background subagents (`async: true`) run in a detached runner that loads ambient extensions by default; the permission system detects their `PI_SUBAGENT_CHILD` / `PI_SUBAGENT_PARENT_SESSION` markers and forwards `ask` prompts to the parent dialog, so they follow the same policy. Local foreground subagents run inside the parent process and `pi-subagents` deliberately does not load ambient extensions into them, so this gate is absent — a foreground child's tool calls are **not** gated by this policy. Delegate work that needs the gate to a background subagent. Verify with step 4 below.

## Install

Run this from the project root you want gated:

```bash
pi install npm:@gotgenes/pi-permission-system -l
```

`-l` (`--local`) writes the package declaration to `.pi/settings.json` in that project instead of `~/.pi/agent/settings.json`, and pi keeps the package under the project's `.pi/npm/`. The gate then exists only in projects where you install it. A global install is not recommended for this extension: the policy belongs to one repository, so one project's rules and risk posture should not silently govern another.

> **The project must be trusted.** Pi loads project packages only after project trust is resolved: the first run in the directory asks, and `/trust` saves the decision for future runs. Declining trust skips `.pi/settings.json` and the policy file, so no gate runs in that session. Print, JSON, and RPC runs have no trust prompt — pass `--approve` (or set `defaultProjectTrust`) or the gate stays off.

> **Project already has protected files?** `.pi/settings.json` and `.pi/extensions/` are protected project resources, so while the directory is untrusted pi refuses to modify project package config — a second install, another project package, or the uninstall below fails with `Project is not trusted. Use --approve to modify local package config.` Add `--approve` to that command, or save trust first with `/trust`.

> **Migrating from a global install?** Remove it first — `pi remove npm:@gotgenes/pi-permission-system` — and delete `~/.pi/agent/extensions/pi-permission-system/`. A global copy runs in every project and still layers its config beneath the project one.

## Configure

Create the policy file inside the project — `<project>/.pi/extensions/pi-permission-system/config.json`:

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
      "*": "allow",
      "rm -rf *": "deny",
      "sudo *": "ask"
    },
    "external_directory": "ask"
  }
}
```

Even with `bash: "*": "allow"`, the deny list above already covers the commands worth stopping, and it keeps the setup usable: the install flow in [`installation.md`](installation.md) hands an agent a handful of `git` and `cp` commands, and `bash: "*": "ask"` would put a dialog in front of every one of them. Switch the catch-all to `"ask"` only if you would rather review each command.

Without this file the extension still loads but its universal fallback is `ask`, so every tool call prompts. Create the policy before relying on the install.

> `bash` patterns match the **command text**, arguments included, so `"rm -rf *": "deny"` matches the whole invocation and `"cat ~/.ssh/*": "deny"` would match too. Put file-path rules on `path` anyway: that surface is the cross-cutting one, so it covers the file tools and MCP as well as bash, it matches relative and absolute spellings of the same file, and it follows symlinks. A `path` deny is what makes step 1 below hold for `read`, `cat`, and any other route to the same file.

The policy path is resolved from pi's **working directory**, not from the package location: start pi from the project root (the directory containing `.pi/`), not a subdirectory.

> **Project config replaces global rules, it does not merely tighten them.** If a global config also exists (for example from an earlier global install), scopes merge in precedence order — global → project → global agent → project agent — and a later scope wins: a project `allow` on a pattern overrides a global `deny` on the same pattern. Safety comes from trust gating (project config loads only after the directory is trusted) and from fail-closed loading: when a non-global scope is present but fails to load or validate, every `allow` is clamped to `ask` instead of silently inheriting the lower scope. Review a repository's permission config before granting it trust.

Then restart pi from the project root, or run `/reload` in the current session.

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

In a fresh `pi` session, started from the project root:

1. Ask the agent to `cat ~/.ssh/id_ed25519` — it should be blocked by the `path` deny.
2. Ask the agent to read `../some-other-project/README.md` — it should raise an `external_directory: ask` dialog, because the path leaves the cwd. This fires for `cat ../other/README.md` too: the gate reads the paths out of a command, so a `bash` rule allowing `cat` does not bypass the boundary.
3. Ask the agent to run `rm -rf node_modules` — it should be blocked by `bash: "rm -rf *": "deny"`.
4. Ask the agent to delegate `rm -rf node_modules` to a **background** sub-agent (`async: true`) — the same block (or prompt, under an `ask` rule) should appear in the parent session, confirming that background child asks are forwarded. Do not use a foreground sub-agent for this check: it does not load the gate (see the note under [Install](#install)).

If any of these pass silently, the gate isn't running or the config isn't being read: confirm you started pi from the project root, the directory is trusted, and the JSON parses (`jq . .pi/extensions/pi-permission-system/config.json`).

## Customize

- **Global fallback** — a config at `~/.pi/agent/extensions/pi-permission-system/config.json` still layers beneath the project one, for rules you want in every gated project. The project file wins on a pattern collision, so a project `allow` overrides a global `deny` — a global file is a fallback, not a safety net.
- **Per-agent overrides** — add frontmatter to an agent definition (`~/.pi/agent/agents/<name>.md`) for role-specific policies, e.g. stricter bash rules for an `explore` agent.
- **Patterns** — `*` is the only wildcard. Approving a similar prompt once makes the dialog offer a generated rule you can reuse.

## Uninstall

Run this from the project root:

```bash
pi remove npm:@gotgenes/pi-permission-system -l --approve
```

`--approve` trusts the protected `.pi/settings.json` for this command; without it pi refuses to edit project package config. This removes the entry from `.pi/settings.json` and the installed copy under `.pi/npm/`. The policy file is left in place — delete it for a fully fresh start:

```bash
rm -rf .pi/extensions/pi-permission-system
```

Review logs always live in the global agent directory, even for project installs: `~/.pi/agent/extensions/pi-permission-system/logs`.

## Scope

- Adds the permission gate, hides disallowed tools before the agent starts, and forwards `ask` prompts from background (detached) child sessions to the parent UI. Local foreground subagents never load ambient extensions, so they run outside this gate.
- Fails closed: an internal gate error blocks the tool rather than letting it through.
- Does not change the main agent's behavior, system prompt, default tool list, model selection, or session lifecycle — remove the gate and pi behaves exactly as before.
