# Mode: Handoff

Prepare for a session break or context handoff.

1. Read `.claude/TASKS.md` for current status.
2. Run `git status` and `git diff --stat` for uncommitted work.
3. Generate handoff prompt:
```
Read CLAUDE.md first, then relevant .claude/ files (see "When to read what" table).
Current status: {{last completed}}
Next up: {{next tasks}}
Blockers: {{unresolved decisions}}
Uncommitted: {{modified files or "none"}}
Context: {{session decisions not yet in docs}}
```
4. Update TASKS.md with latest status.
5. Update CHANGELOG.md if doc changes were made.
