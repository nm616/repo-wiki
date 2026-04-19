# repo-wiki

A Claude Code plugin that gives Claude **persistent project memory** across every session.

Inspired by the [Karpathy LLM Wiki pattern](https://karpathy.ai) — instead of ingesting external documents, `repo-wiki` captures what Claude learns across your conversations: what the project is, decisions made and why, and what happened each session. Knowledge compounds. Nothing important is lost to context compression or new sessions.

---

## How It Works

On first use, `repo-wiki` creates two things in your project:

**1. A structured wiki at `.claude/wiki/`**
```
.claude/wiki/
├── SCHEMA.md           — Wiki conventions
├── index.md            — Page catalog (updated after every write)
├── log.md              — Append-only chronological event log
├── project.md          — Project overview, goals, tech stack
├── decisions/          — Architectural decision records (ADR-style)
│   └── YYYY-MM-DD-slug.md
└── sessions/           — Per-session conversation summaries
    └── YYYY-MM-DD-slug.md
```

**2. A `## Project Wiki` section in your `CLAUDE.md`**

This is the key piece. `CLAUDE.md` is automatically loaded into every Claude session. The injected section instructs Claude to:
- Read `project.md` at the start of every session for project context
- Proactively check `index.md` when it needs context from previous work
- Never ask the user for context that may already be in the wiki

No prompting required. Claude just knows.

---

## Installation

### Option 1: Official marketplace (recommended)

repo-wiki is submitted to the Anthropic plugin marketplace. Once listed, install it from inside Claude Code:

```
/plugin install repo-wiki
/reload-plugins
```

### Option 2: Local install from source

```bash
git clone https://github.com/nm616/repo-wiki.git
claude --plugin-dir ./repo-wiki
```

Or to install permanently, copy the directory to your Claude plugins folder and enable it via `/plugin`.

### Verify installation

After loading, run `/help` — you should see `repo-wiki:update` listed under skills. The plugin activates automatically on your next project session.

---

## First Use

Run on any project to initialize the wiki:

```
/repo-wiki:update
```

This creates `.claude/wiki/` and writes the `## Project Wiki` section to your `CLAUDE.md`. From that point forward, Claude will proactively reference the wiki in every session — no further setup needed.

---

## Commands

| Command | What it does |
|---------|-------------|
| `/repo-wiki:update` | Save the current session to the wiki |
| `/repo-wiki:update auth refactor` | Save with a specific topic as the title |

---

## Plugin Components

### Skills

**`repo-wiki`** — Auto-activating reference skill. Triggers on "catch me up", "what decisions were made", "how does the wiki work", etc. Provides full documentation on wiki structure and conventions.

**`update`** — The `/repo-wiki:update` slash command. Triggers the `wiki-archivist` agent to record the current session.

### Agent: `wiki-archivist`

Does all wiki reads and writes. On every update it:
- Initializes the wiki if it doesn't exist (including CLAUDE.md injection)
- Writes session summaries to `sessions/`
- Creates decision records in `decisions/` when architectural choices are made
- Updates `project.md` when project scope evolves
- Always updates `index.md` last
- Only appends to `log.md` — never edits earlier entries

### Hooks (fully automatic)

**`PreCompact`** — Fires before context compression. Captures the session so nothing is lost when the context window resets.

**`Stop`** — Fires at session end. Archives the session if significant work was done. Skips if the session was trivial.

---

## What Gets Recorded

**Session summaries** (`sessions/`) — What was built or changed, what was attempted, open questions for next time. Written automatically at session end.

**Decision records** (`decisions/`) — Architectural choices, library selections, "why we did it this way." The most durable and valuable content. Written whenever an architectural decision is made.

**Project overview** (`project.md`) — What the project is, its goals, tech stack, key constraints. Updated when scope evolves.

---

## Committing the Wiki

The wiki lives at `.claude/wiki/` inside your project. You control whether it's committed:

```bash
# Share wiki with collaborators (commit it)
git add .claude/wiki/
git commit -m "add project wiki"

# Keep wiki private (add to .gitignore)
echo ".claude/wiki/" >> .gitignore
```

The `CLAUDE.md` section is always safe to commit — it contains only instructions, no sensitive content.

---

## Relationship to Auto-Memory

Claude Code's built-in `memory/` system handles user preferences and feedback across all projects. `repo-wiki` handles project-specific knowledge (decisions, sessions, project overview) and lives inside each project. Different jobs, no conflict.

---

## Inspired By

The [LLM Wiki pattern](https://karpathy.ai) described by Andrej Karpathy: instead of re-deriving knowledge from raw sources at every query, an LLM incrementally builds and maintains a persistent, structured wiki. The wiki keeps getting richer with every session. `repo-wiki` applies this pattern to software project memory inside Claude Code.
