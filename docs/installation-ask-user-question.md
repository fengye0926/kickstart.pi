# Install ask-user-question for pi

[@juicesharp/rpiv-ask-user-question](https://www.npmjs.com/package/@juicesharp/rpiv-ask-user-question) lets the model ask you instead of guessing. It adds one tool — `ask_user_question` — that opens a terminal dialog of up to four questions with authored options and hands your choices back as structured data. Install it if you would rather spend fifteen seconds picking than an hour undoing a wrong assumption.

## Install

```bash
pi install npm:@juicesharp/rpiv-ask-user-question
```

This adds the extension to your global pi settings (`~/.pi/agent/settings.json`). Pi auto-discovers it on startup.

> Install at the **global** level: the tool is a session-level capability — one install covers every project.

## Verify

In a fresh `pi` session, hand the model a task with a real decision buried in it, e.g. *"Add caching to the API client."* Instead of picking a strategy on its own, it calls `ask_user_question` and a dialog takes over the bottom of the terminal.

While the dialog is open:

- `↑` / `↓` move between options, `Enter` chooses.
- `n` attaches a note to a question — or a global note to the whole questionnaire from the Submit tab.
- The `Type something.` row answers in your own words; `Shift+Enter` adds a line, `Ctrl+G` opens Pi's external editor, `Ctrl+U` clears the draft.
- `Tab` moves between questions; the Submit tab reviews everything before it goes back.
- `Ctrl+]` collapses the overlay so you can scroll the transcript, then brings it back.
- `Esc` abandons the questionnaire.

No setup required — the tool is live as soon as Pi restarts.

## Activate

Restart pi, or run `/reload` inside an existing pi session.

## Configure

Optional. Create `~/.config/rpiv-ask-user-question/config.json`:

```json
{ "collapseKey": "alt+o" }
```

| Setting | What it does | Default |
| --- | --- | --- |
| `collapseKey` | Key that collapses and expands the dialog. `"off"` disables the shortcut. | `"ctrl+]"` |
| `guidance.description` | Full replacement for the tool description the model sees. | built-in description |
| `guidance.promptSnippet` | One-line description of the tool in the system prompt — tune how eagerly the model asks. | built-in snippet |
| `guidance.promptGuidelines` | Usage guidelines given to the model, as a list of strings. | 4 built-in guidelines |

Malformed JSON falls back to defaults with a warning; an unusable value is silently dropped back to its default.

## Uninstall

```bash
pi remove npm:@juicesharp/rpiv-ask-user-question
```

## Scope

- Requires Node.js 22 or newer and an interactive terminal (or an RPC/ACP host such as the VS Code pendant or Zed).
- Non-interactive runs never see the tool — it is removed from the model's tool list instead of failing every call.
- Side-by-side option previews need a terminal at least 100 columns wide; narrower terminals stack the preview under the options.
- No native dependencies, no compiler, no API keys — the extension makes no model calls of its own.
- Source and full reference: [juicesharp/rpiv-mono](https://github.com/juicesharp/rpiv-mono/tree/main/packages/rpiv-ask-user-question).
