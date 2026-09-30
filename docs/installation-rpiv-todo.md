# Install rpiv-todo for pi

[@juicesharp/rpiv-todo](https://www.npmjs.com/package/@juicesharp/rpiv-todo) gives the model a task list you can see. It adds a `todo` tool, a `/todos` command, and a live panel above the editor showing what is completed, what is in progress, and what is queued. The list is rebuilt from the conversation itself, so it survives `/reload` and compaction — useful on long research → design → implement sessions.

## Install

```bash
pi install npm:@juicesharp/rpiv-todo
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: the todo panel is a session-level view — one install covers every project.

## Verify

In a fresh `pi` session:

- Run `/todos` — on a fresh session it prints `No todos yet. Ask the agent to add some!`
- Ask for a multi-step task, e.g. *"add a repository layer with tests, and track it as todos"* — the model calls `todo` and the panel appears above the input box.
- Press `ctrl+shift+t` to collapse and expand the panel.

Everything the model records stays keyed by session, so parallel or child sessions can neither read nor overwrite the foreground list.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Configure

Optional. Create `~/.config/rpiv-todo/config.json` (or `$XDG_CONFIG_HOME/rpiv-todo/config.json`):

```json
{
  "maxWidgetLines": 8,
  "collapseKey": "alt+t"
}
```

| Setting | What it does | Default |
| --- | --- | --- |
| `maxWidgetLines` | Content rows the overlay may use, heading included. Minimum `3`. | `12` |
| `collapseKey` | Key that collapses and expands the panel, in Pi keybinding form. Set `"off"` to register no shortcut. Needs `/reload` to rebind. | `"ctrl+shift+t"` |
| `guidance` | Replaces the built-in instructions the extension gives the model about when and how to use the list. Needs `/reload`. | built-ins |

The file is read, never written. A missing or malformed file falls back to defaults.

**Localized UI:** install [`@juicesharp/rpiv-i18n`](https://www.npmjs.com/package/@juicesharp/rpiv-i18n) to render the panel chrome in your language and get a `/languages` picker. Without it, everything falls back to English.

## Uninstall

```bash
pi remove npm:@juicesharp/rpiv-todo
```

## Scope

- No API key, no model selection, no native dependencies.
- An interactive session is required for the panel and `/todos`; headless runs still get the `todo` tool, but nothing is rendered.
- Source and full reference: [juicesharp/rpiv-mono](https://github.com/juicesharp/rpiv-mono/tree/main/packages/rpiv-todo).
