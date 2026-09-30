# Install pi-open-tui for pi

[pi-open-tui](https://pi.dev/packages/pi-open-tui) is a TUI styling package for pi: an animated Pi-logo header, a two-line [Starship](https://starship.rs/)-style footer (cwd, git branch & status, runtime version, context bar, model, tokens, cost), a rounded editor with an accent rail, a working timer, and per-turn telemetry (TPS / TTFT / stalls). Everything is configured from the interactive `/open-tui` dialog.

## Install

```bash
pi install npm:pi-open-tui
```

The package is recorded in your global pi settings (`~/.pi/agent/settings.json`) and picked up on startup.

> Install at the **global** level: the header, footer, and editor chrome are session-level UI — one install covers every project.

To try it for a single run without installing:

```bash
pi -e npm:pi-open-tui
```

## Verify

Start a fresh `pi` session and check for:

- the animated header at the top of the TUI — the Pi logo cycling through 16 color frames, with a "Let's build something great" tagline;
- the two-line footer showing cwd, git branch & status, runtime version, context bar, model, token counts, and cost;
- the rounded editor with its accent rail framing the input.

## Activate

Restart pi, or run `/reload` in the current session.

## Configure

Run `/open-tui` to open the tabbed settings dialog. `Tab` / `Shift+Tab` cycle through four sections:

- **Features** — header, footer, rounded editor, telemetry, …
- **Icons** — `auto` (detect Nerd Font), `nerd` (force Nerd Font glyphs), `ascii` (plain fallbacks)
- **Segments** — which footer segments are visible (cwd, git branch, git status, git commit hash, runtime, context, tokens, cost)
- **Telemetry** — the post-turn notification and its parts (TPS, TTFT, duration, tokens, stalls, cost)

Settings live in `~/.pi/agent/open-tui.json`. Missing or invalid values fall back to the package defaults.

## Uninstall

```bash
pi remove npm:pi-open-tui
```

This removes the entry from `~/.pi/agent/settings.json`; other packages and `~/.pi/agent/open-tui.json` stay in place.

## Scope

pi-open-tui only changes TUI chrome. It does not affect model behavior, tool calls, or prompt contents.
