---
name: update
description: This skill is invoked by the user as /repo-wiki:update to manually trigger a wiki update for the current session. Records session context, decisions made, and updates the project wiki at .claude/wiki/. Accepts an optional argument describing the topic or decision to emphasize.
argument-hint: "[optional: topic or decision to highlight]"
version: 0.1.0
---

# Update Wiki

When this skill is invoked, trigger a comprehensive wiki update for the current session.

## Process

1. **Assess session content** — Reflect on the current conversation:
   - What was built, changed, or fixed?
   - What decisions were made and why?
   - What was attempted but abandoned, and why?
   - What is the project's current state?
   - What should the next session know?

2. **Invoke wiki-archivist** — Use the `wiki-archivist` agent with the following instructions:
   - Initialize the wiki first if `.claude/wiki/` does not exist
   - Write a session summary to `sessions/YYYY-MM-DD-<topic>.md`
   - Write decision records to `decisions/YYYY-MM-DD-<slug>.md` for any architectural decisions made this session
   - Update `project.md` if the project scope or understanding has changed
   - Append to `log.md` with type `session`
   - Update `index.md` last

3. **Report** — After the archivist completes, briefly confirm what was written and where.

## With Optional Argument

If the user provides a topic argument (e.g., `/repo-wiki:update auth refactor`), pass that topic to the archivist as the primary focus for the session entry title and content.

## Quality Criteria for a Good Update

- **What**: What was accomplished or changed in concrete terms
- **Why**: The reasoning behind any decisions
- **How**: Architecture or approach used (not line-by-line details)
- **Next**: Open questions or logical next steps for the next session

Keep session entries concise (~200-400 words). A future Claude instance should be able to read it in 30 seconds and have full context.
