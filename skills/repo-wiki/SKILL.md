---
name: repo-wiki
description: This skill should be used when the user asks "catch me up on this project", "what does this project do", "what decisions were made", "what's the context here", "project history", "what was decided", "summarize what we've done", "how does the wiki work", "what's in the wiki", or when detailed guidance is needed on reading or using the repo wiki at .claude/wiki/. Note: the CLAUDE.md in each project already instructs Claude to check the wiki proactively — this skill provides the full reference on wiki structure and conventions.
version: 0.1.0
---

# Repo Wiki

The repo-wiki plugin maintains a persistent structured wiki at `.claude/wiki/` inside each project. The wiki compounds knowledge across sessions — decisions, session summaries, and project context survive context compression and new sessions.

## Wiki Structure

```
.claude/wiki/
├── SCHEMA.md           — Wiki conventions and rules
├── index.md            — Page catalog with one-line summaries
├── log.md              — Append-only chronological event log
├── project.md          — Project overview, goals, tech stack
├── decisions/          — Architectural and design decisions (ADR-style)
│   └── YYYY-MM-DD-slug.md
└── sessions/           — Per-session conversation summaries
    └── YYYY-MM-DD-slug.md
```

## Reading the Wiki at Session Start

When starting a new session or needing project context:

1. Check if `.claude/wiki/` exists in the current project directory
2. Read `index.md` first — this is the catalog of all pages
3. Read `project.md` for the project overview and tech stack
4. Check the last 3-5 entries in `log.md` to see recent activity
5. Drill into specific `decisions/` pages if relevant to today's task
6. Skim recent `sessions/` entries for recent work context

If `.claude/wiki/` does not exist, the wiki will be initialized automatically on the first `/repo-wiki:update` or at session end.

## What Each Page Type Contains

**`project.md`** — What the project is, its goals, tech stack, key constraints. The starting point for any new session.

**`decisions/`** — Architectural choices: library selections, design patterns chosen, "why we did it this way" records. The most durable and valuable content.

**`sessions/`** — What happened in a session: what was built, what was attempted, open questions for next time. Ephemeral but useful for the first few sessions after.

**`index.md`** — Always updated last after any write. The navigation hub.

**`log.md`** — Append-only timeline of when things happened. Never edited, only appended.

## Writing to the Wiki

Always delegate wiki writes to the `wiki-archivist` agent. Never write wiki pages directly — the archivist handles index updates, cross-links, and log entries correctly.

The wiki updates automatically via hooks (PreCompact before compression, Stop at session end). Manual updates are available via `/repo-wiki:update`.

## Quick Reference

| Need | Action |
|------|--------|
| Restore session context | Read `index.md` then `project.md` |
| Find a past decision | Read `index.md`, then the relevant `decisions/` page |
| Understand recent work | Read last 3-5 entries in `log.md`, then relevant `sessions/` pages |
| Save current context | `/repo-wiki:update` or wait for automatic session-end hook |
| Initialize wiki | Run `/repo-wiki:update` on a project with no wiki yet |

## Additional Resources

The `wiki-archivist` agent has the full SCHEMA template and all page formats embedded in its system prompt. No external template files need to be read — the agent is self-contained and can initialize a wiki from scratch in any project.
