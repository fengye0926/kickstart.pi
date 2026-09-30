# Install frontend-image-to-code for pi

[frontend-image-to-code](../skills/frontend-image-to-code/SKILL.md) is a Pi skill bundled in this repository. Give the agent a screenshot, mockup, Figma export, local reference image, or a design annotation link (Figma / MasterGo / Lanhu), and it follows one loop:

```
design image input
→ image analysis
→ implementable spec
→ map onto the existing project
→ implement
→ render and compare
→ visual iteration
```

The skill treats your image as the single source of visual truth, keeps the project's own stack, components, routing, business logic, control semantics, and permission checks, and requires at least one rendered comparison before reporting the task complete. Sizes follow an evidence chain — design annotations first, then design tokens, then measurement from the high-resolution image — and anything the design doesn't cover is extrapolated minimally and reported as an implementation inference. The skill text is written in Chinese.

## Install

The skill lives at [`skills/frontend-image-to-code/`](../skills/frontend-image-to-code) in this repository. From a clone of the repo:

```bash
mkdir -p ~/.pi/agent/skills
cp -R skills/frontend-image-to-code ~/.pi/agent/skills/
```

The layout after copying:

```
~/.pi/agent/skills/
└── frontend-image-to-code/
    └── SKILL.md
```

> Install at the **global** level to use it in every project, or copy it into a project's `.pi/skills/` directory to scope it to that repository.

## Verify

In a fresh `pi` session:

- Run `/skill:frontend-image-to-code` — the command loads the skill explicitly.
- The skill also auto-loads when a request matches its description (screenshot / mockup / Figma export → frontend code).
- Attach a small reference image and ask for it to be translated into an existing page; confirm the agent reads the image first and inspects the target project before editing.

## Activate

Restart pi, or run `/reload` inside an existing pi session after copying the files.

## Configure

Optional. Add a routing hint to your global `~/.pi/agent/AGENTS.md` so the agent reaches for the skill deliberately:

```markdown
## Frontend workflow

- For requests that include a screenshot, mockup, Figma export, or visual reference and ask to translate that specific interface into frontend code, use `frontend-image-to-code`.
```

The skill works without a browser, but a rendered comparison is part of its completion criteria. [agent-browser](installation-agent-browser.md) (global) is the most direct way to produce that screenshot.

## Uninstall

```bash
rm -rf ~/.pi/agent/skills/frontend-image-to-code
```

## Scope

- One responsibility: image-to-code. It does not invent a new visual direction, do general project planning, review code, or build a design system.
- Images (plus design annotations, when provided) are the visual fact source; implementation details not visible in them are kept to the minimum required for the feature to function and are reported as assumptions.
- Rendered comparison fixes viewport / DPR, font loading, locale, permissions, mock data, and animation state before judging differences, and stops after two rounds without progress instead of polishing endlessly.
- The delivery report lists remaining differences against the design and any extrapolated states.
- The skill follows the Agent Skills standard (`name` + `description` frontmatter) and is self-contained in a single `SKILL.md`.
