# Idea Skill

Quick capture of one voiced idea into a provenance-tracked folder in your ideas repo. Part of the Personal Corp framework.

## What it does

One invocation, one idea captured end-to-end:

1. Gets the real date (handles midnight edge cases).
2. **Deduplicates** against `INDEX.md` using semantic matching — not string equality. "Advisory board built on agents" matches an existing `advisory-agent-grounded-data` folder and appends a new source row instead of creating a duplicate.
3. If the idea is new: creates a folder `ideas/YYYY-MM-DD-{slug}/` from templates (four files: `idea.md`, `sources.md`, `market.md`, `next-steps.md`). Slug is 3–5 kebab-case words derived from meaning, not form.
4. Populates `idea.md` with raw verbatim wording (`status: seedling`), a 1–2 sentence essence summary, and provenance. Never rewrites the raw idea into architecture.
5. Regenerates `INDEX.md`.
6. Optionally creates a GitHub Issue mirror and adds it to your Ideas board (see **Setup**).
7. Commits and pushes to your ideas repo.

**Repeat sighting of an existing idea** (dedup match): appends a row to `sources.md` of the existing folder, updates `last_touch` and increments `sources_count` in `idea.md`, optionally comments on the mirror issue — then stops. No new folder is created.

## Why you'd want it

Without a structured capture flow:
- Voiced ideas vanish between sessions — you can't reconstruct when or why you had them.
- You re-create ideas you already explored, wasting exploration time.
- There is no provenance — you can't tell if an idea came from a client meeting, a reading session, or a solo voice draft.
- Ideas and tasks get mixed in the same tracker, cluttering your work queue.

The skill enforces provenance on every entry, deduplicates before writing, and keeps ideas separated from dated work tasks.

## When to use

- You say "capture this idea", "I have an idea", "record idea", `/idea`, or `idea: ...`
- You want to log a single idea immediately without filling out a form

**Do NOT use for:**
- Mining multiple ideas from a transcript or meeting recording — use a separate extraction skill for that.
- Dated work tasks with deadlines or GitHub tracking — use `manager` for those.

## What you get

- A folder `ideas/YYYY-MM-DD-{slug}/` in your ideas repo containing:
  - `idea.md` — title, status, essence, raw verbatim wording, frontmatter
  - `sources.md` — provenance table (date / who / context / source link / notes)
  - `market.md` — analogues, potential, competitive signals (filled in later)
  - `next-steps.md` — open questions, what to do next (e.g. landing? brainstorm? park?)
- An updated `INDEX.md` listing all ideas with status, first-voiced date, sources count, last touch
- *(Optional)* A GitHub Issue mirror titled `idea: {title}` with label `idea`, body = folder link + essence + status + provenance line; issue added to your Ideas board view

A one-line report per step: folder path, issue number (if mirror enabled), board URL.

## Installation

```bash
cp -r skills/idea ~/.claude/skills/
```

The skill is then available in Claude Code.

## Setup

Add an `## Idea Config` section to your project's `CLAUDE.md` (or your global `~/.claude/CLAUDE.md`):

| Config key | Purpose | Example |
|---|---|---|
| `ideas_repo` | Absolute path to your local ideas repository | `~/Documents/GitHub/my-ideas` |
| `default_author` | Name recorded as `first_voiced_by` when not specified | `Me` |
| `owner` | Your GitHub handle — used for issue and project commands (only if `mirror_enabled: true`) | `your-github-handle` |
| `ideas_repo_name` | GitHub repo name where mirror issues are created (only if `mirror_enabled: true`) | `ideas` |
| `mirror_enabled` | Enable the GitHub Issue + Project mirror (`true` / `false`) | `false` |
| `project_number` | GitHub Project number for your Ideas board (only if `mirror_enabled: true`) | `7` |

Mirror issues always use the label `idea` (hardcoded — don't invent others).

Minimal working config (no GitHub mirror):

```markdown
## Idea Config
- ideas_repo: ~/Documents/GitHub/my-ideas
- default_author: Me
- mirror_enabled: false
```

Full config with mirror:

```markdown
## Idea Config
- ideas_repo: ~/Documents/GitHub/my-ideas
- default_author: Me
- owner: your-github-handle
- ideas_repo_name: ideas
- mirror_enabled: true
- project_number: 7
```

Without config the skill will ask for the repo path on first run.

## How to invoke

**Capture a new idea:**

> "capture this idea: [your idea]"

or

> `/idea [your idea]`

or just say it naturally:

> "I have an idea — [your idea]"

The skill:
1. Asks **at most one** clarifying question — only if the essence (what it is / who it's for) cannot be inferred from the wording. Never asks about mechanics, pricing, risks, or stack: those go into `next-steps.md` as open questions.
2. Runs dedup against `INDEX.md`.
3. Creates the folder (or appends to existing) → updates INDEX → optionally creates mirror → commits.
4. Returns a short report.

**Provenance kinds recognized:**

| Source | What to say / how it appears in sources.md |
|---|---|
| Direct invocation | `direct skill invocation, {date}, Claude Code session` |
| Meeting note | `meeting note, {date}, {person or context}` |
| CRM / deal context | `CRM card, {date}, {deal or person}` |
| DM / chat | `DM, {date}, {channel or context}` |
| Voice draft | `voice draft, {date}` |
| Web reading | `{URL}, {date}` |

## Status enum (in `idea.md` frontmatter)

| Status | Meaning |
|---|---|
| `seedling` | Just voiced, not developed yet |
| `exploring` | Initial research done, `market.md` filled |
| `building` | Landing page / pilot / prototype in progress |
| `live` | Public artifact exists |
| `parked` | Paused; reason in `next-steps.md` |
| `dropped` | Closed; reason in `next-steps.md` |

New ideas always start at `seedling`.

## INDEX.md format

`INDEX.md` is auto-generated — do not edit by hand. The skill regenerates it after every write. Structure:

```markdown
# ideas index

Updated: YYYY-MM-DD

## Active ideas
| Slug | Status | First voiced | Sources count | Last touch |
|---|---|---|---|---|
| live-sales-coach-b2b | seedling | 2026-05-01 | 1 | 2026-05-01 |

## Parked / dropped
...

## By status
- seedling: N
- exploring: N
- building: N
- live: N
- parked: N
- dropped: N
```

## See also

- [SKILL.md](SKILL.md) — full algorithm specification, dedup rules, mirror commands, output templates
- [README.ru.md](README.ru.md) — Russian version
- `manager` — for dated work tasks with GitHub Issues tracking
