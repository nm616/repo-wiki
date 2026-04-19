# Wiki Schema

Conventions for `.claude/wiki/`. Follow these rules when adding or updating pages.

## Directory Structure

```
.claude/wiki/
├── SCHEMA.md       — This file: wiki conventions
├── index.md        — Page catalog (always updated last after any write)
├── log.md          — Append-only chronological event log
├── project.md      — Project overview, goals, tech stack
├── decisions/      — Architectural decision records (ADR-style)
└── sessions/       — Per-session conversation summaries
```

## Page Frontmatter

All pages use YAML frontmatter:

```yaml
---
title: Page Title
date: YYYY-MM-DD
type: session | decision | project
tags: [tag1, tag2]
---
```

## project.md

Free-form markdown covering:
- What the project is and does
- Goals and intended users
- Tech stack and key dependencies
- Key constraints or non-obvious decisions
- Links to external docs (if any)

Updated when scope or understanding changes. Never rewritten from scratch — revise or append sections.

## decisions/YYYY-MM-DD-slug.md

Four sections in order:

1. **Context** — What situation prompted this decision
2. **Decision** — What was decided, in one clear sentence
3. **Rationale** — Why this was chosen; alternatives considered and rejected
4. **Consequences** — Tradeoffs, constraints introduced, follow-up work

Status field: `accepted`, `superseded`, `deprecated`
When superseded: update `status` and add a link to the new decision file.

## sessions/YYYY-MM-DD-slug.md

Five sections:

1. **Summary** — 2-3 sentence overview
2. **What Was Done** — Bullet list of concrete work
3. **Decisions Made** — Bullet list of decisions, or "None"
4. **Approach and Rationale** — Key architectural/design choices
5. **Open Questions / Next Steps** — What remains unresolved

Target length: 200-400 words. Focus on what future-Claude needs to know, not a transcript.

## Log Format

`log.md` is append-only. Never edit earlier entries.

```
## [YYYY-MM-DD] <type> | <title>

<One sentence description.>
```

Entry types: `init`, `session`, `decision`, `pre-compact`, `session-end`

## Index Format

`index.md` groups pages by category, most recent first within each group:

```markdown
# Wiki Index

## Project
- [project.md](project.md) — One-line description

## Sessions
- [Title](sessions/YYYY-MM-DD-slug.md) — One-line summary

## Decisions
- [Title](decisions/YYYY-MM-DD-slug.md) — One-line summary
```

## Cross-Linking

Use relative paths from the wiki root or the current file's directory:
- From index.md: `[text](decisions/file.md)`
- From sessions/: `[text](../decisions/file.md)`
- From decisions/: `[text](../sessions/file.md)`

## Maintenance Rules

- After every write: update `index.md` and append to `log.md`
- Never edit `log.md` entries, only append
- Never delete pages; mark decisions `status: superseded` with a link to the replacement
- Size guidance: project.md up to 1,000 words; sessions 200-400 words; decisions 150-300 words
