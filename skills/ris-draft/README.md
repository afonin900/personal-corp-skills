# Ris Draft Skill

Generates a single self-contained HTML page with a technical diagram in flat engineering blueprint style — printed spec sheet aesthetic, not marketing landing.

## What it does

Tell the agent "draw me a system architecture" — get one `.html` file that opens in any browser and looks like a printed engineering doc: black-and-white outlines, monospace labels, no shadows or gradients, no internet dependencies.

No external fonts, no CDN — works fully offline.

## When to use

- **System architecture** — services, data flow, dependencies
- **System flow** — process stages left-to-right or top-to-bottom with decision diamonds
- **Technical spec sheet** — grid of cells with mono values + sans-serif labels
- **Component map** — nested boxes, parent → children with separator lines

Not for:
- Inline schemas in markdown docs (use a mermaid renderer)
- Newspaper-style single-column reading pages
- Multi-section interactive explainers with navigation

## Output

One file (e.g. `diagram.html`) — opens in any browser, exportable to PDF, embeddable in slides as iframe or screenshot.

Contents:
- Title + UPPERCASE subtitle (mono)
- Diagram inside `.diagram-canvas` (bordered, generous padding)
- Sans-serif for labels, monospace for data/paths/codes/IDs
- Solid connectors = structural, dashed = logical/abstract
- Optional outlined or filled badges with short uppercase tags

## Installation

```bash
cp -r skills/ris-draft ~/.claude/skills/
```

The skill is then available in Claude Code.

## How to invoke

Tell the agent in natural language:

> "Draw me a blueprint of my publishing system: incoming sources → parsing → distillation → file in folder. Output to /tmp/pipeline.html"

Or:

> "Make a technical spec sheet for the weekly digest pipeline"

Or use the slash command: `/ris-draft`

The agent will:
1. Ask 1-2 clarifying questions if input is incomplete (diagram type, node list)
2. Generate one HTML file following the fixed visual canon
3. Output a file ready to open in browser — no build step, no dependencies

## Style rules (baked into the skill)

- Flat: no shadows, gradients, glassmorphism, blur
- Outlined: 1-2px solid borders define structure
- Monochrome: black + gray + one semantic accent (e.g. red for error) used sparingly
- System fonts only: no Google Fonts, no CDN
- All CSS inline: one `<style>` block in head, no separate files
- No JS: only if explicitly requested

## See also

- [SKILL.md](SKILL.md) — full skill specification with CSS tokens and HTML template
- [README.ru.md](README.ru.md) — Russian version
