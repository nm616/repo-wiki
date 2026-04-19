## Project Wiki

This project maintains a persistent knowledge wiki at `.claude/wiki/`.

- At the start of every session, read `.claude/wiki/project.md` for project context.
- When you need context from previous work, decisions, or sessions that is not in the current conversation, check `.claude/wiki/index.md` to find relevant pages, then read them.
- Do not ask the user for context that may be in the wiki — look it up proactively.
- To save context manually: `/repo-wiki:update`
