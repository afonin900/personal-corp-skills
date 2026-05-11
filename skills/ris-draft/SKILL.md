---
name: ris-draft
description: Use when user wants a standalone HTML diagram in flat engineering blueprint style — architecture diagrams, system flows, technical spec sheets, component maps. Generates one self-contained HTML file with inline CSS, no external CDN. User-invoked only — do NOT auto-trigger. Triggers on "/ris-draft", "сделай blueprint", "технический чертёж", "архитектурная схема", "инженерная схема", "blueprint diagram", "engineering blueprint", "technical spec sheet", "architecture diagram", "system flow diagram".
---

# ris-draft — Flat Engineering Blueprint Diagrams

Generate one self-contained HTML page that renders a technical diagram in a strict flat-blueprint aesthetic — the look of a printed engineering specification sheet, not a marketing landing.

**Use this when** the user wants an architecture diagram, system flow, technical spec sheet, or component map as a standalone HTML artifact (suitable for slides, reports, exports).

**Don't use this for:**
- Inline schemas inside markdown documents — use a mermaid renderer instead
- Newspaper / reading-first single-column pages with monospace ink-on-cream feel
- Multi-section interactive explainers with pill navigation

## Design philosophy

Precise. Objective. High data-ink ratio (Tufte). Every pixel earns its place; nothing decorative.

## Visual rules

### Flat, outlined, monochrome

- **No** drop shadows, gradients, glassmorphism, blur, rounded buttons
- 1px or 2px solid borders define structure
- White content blocks on a light-gray canvas
- Accent: black, or a single semantic color (red for error, etc.) used sparingly

### Color tokens

```css
:root {
  --c-bg: #f8fafc;          /* page background — slate-50 */
  --c-canvas: #ffffff;      /* diagram canvas */
  --c-border: #cbd5e1;      /* slate-300 */
  --c-text-main: #0f172a;   /* slate-900 */
  --c-text-sub: #64748b;    /* slate-500 */
  --font-ui: system-ui, -apple-system, 'Segoe UI', sans-serif;
  --font-mono: 'SF Mono', Monaco, Consolas, monospace;
}
```

### Typography

- Headings, labels: sans-serif (system UI stack)
- Data, paths, code, IDs, version strings: monospace

### Layout

- Whole diagram lives in a `.diagram-canvas` — bordered box with generous padding (32px+)
- Header: title + UPPERCASE subtitle, separated from body by a 1px bottom border
- Strict alignment via grid or flexbox; no eyeballing

### Connectors

- Thin straight or orthogonal lines (1px solid)
- Dashed lines for abstract / logical relationships, never structural ones
- SVG for non-orthogonal arrows; CSS pseudo-elements fine for orthogonal

### Icons & badges

- Icons: simple stroke SVG, no fills, no detail
- Badges: outlined or solid black/gray block, small uppercase mono text

## Hard requirements

1. **System fonts only** — never link Google Fonts or any external CDN
2. **Return only** the HTML — no markdown wrapper, no commentary outside the file
3. **Complete document** — `<!DOCTYPE html>` through `</html>`, self-contained
4. **All CSS inline** in a `<style>` block — no separate stylesheet
5. **No JavaScript** unless the user explicitly asks for an interactive diagram

## Output template

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>[Diagram Title]</title>
    <style>
      :root {
        --c-bg: #f8fafc;
        --c-canvas: #ffffff;
        --c-border: #cbd5e1;
        --c-text-main: #0f172a;
        --c-text-sub: #64748b;
        --font-ui: system-ui, -apple-system, 'Segoe UI', sans-serif;
        --font-mono: 'SF Mono', Monaco, Consolas, monospace;
      }

      * { margin: 0; padding: 0; box-sizing: border-box; }

      body {
        background: var(--c-bg);
        font-family: var(--font-ui);
        color: var(--c-text-main);
        padding: 40px;
      }

      .diagram-canvas {
        background: var(--c-canvas);
        border: 2px solid var(--c-border);
        padding: 32px;
        max-width: 1200px;
        margin: 0 auto;
      }

      .diagram-header {
        border-bottom: 1px solid var(--c-border);
        padding-bottom: 16px;
        margin-bottom: 24px;
      }

      .diagram-title {
        font-size: 24px;
        font-weight: 600;
        color: var(--c-text-main);
      }

      .diagram-subtitle {
        font-size: 12px;
        font-weight: 500;
        color: var(--c-text-sub);
        text-transform: uppercase;
        letter-spacing: 0.5px;
        margin-top: 4px;
      }

      /* Diagram-specific styles below */
    </style>
  </head>
  <body>
    <div class="diagram-canvas">
      <div class="diagram-header">
        <h1 class="diagram-title">[Title]</h1>
        <p class="diagram-subtitle">[SUBTITLE IN UPPERCASE]</p>
      </div>

      <!-- Diagram content -->
    </div>
  </body>
</html>
```

## Reusable component snippets

### Node / box

```css
.node {
  background: var(--c-canvas);
  border: 1px solid var(--c-border);
  padding: 12px 16px;
}
```

### Badge

```css
.badge {
  display: inline-block;
  font-size: 10px;
  font-family: var(--font-mono);
  padding: 2px 6px;
  border: 1px solid var(--c-text-main);
  text-transform: uppercase;
}

.badge--filled {
  background: var(--c-text-main);
  color: var(--c-canvas);
}
```

### Connector

```css
.connector {
  border-top: 1px solid var(--c-border);
}

.connector--dashed {
  border-top-style: dashed;
}
```

### Monospace data text

```css
.mono {
  font-family: var(--font-mono);
  font-size: 13px;
}
```

## Composition guide

- **Architecture diagram:** services as nodes, data flow as solid connectors, dependencies as dashed
- **System flow:** linear stages left-to-right or top-to-bottom; decision points as outlined diamonds
- **Spec sheet:** grid of labeled cells, each with a mono value and a sans-serif label; status badge top-right
- **Component map:** nested boxes, parent → children with 1px separator lines, badges on each node

## Quality bar

1. Every text item earns its space — no decorative copy
2. Alignment is strict — no off-grid placement
3. Mono and sans roles never bleed (don't put labels in mono or data in sans)
4. Color usage stays monochrome unless one semantic accent is justified
5. Document is fully self-contained — opens in any browser offline
6. No external network requests at any point

## When input is incomplete

- **No content** → ask for the diagram type (architecture / flow / spec sheet) and the items to render
- **Vague subject** → propose a node list and ask for confirmation before rendering
- **User asks for a specific framework / library** → push back: this skill is HTML+CSS only
