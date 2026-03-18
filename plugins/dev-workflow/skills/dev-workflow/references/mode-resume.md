# Mode: Resume

Catch up on changes since last session before starting new work.

1. Read `.claude/TASKS.md` for last status and next steps.
2. `git log` since last CHANGELOG.md entry date. Summarize: commits, new branches/PRs, new/deleted files.
3. Check for drift: ARCHITECTURE.md vs actual file tree, new deps not in docs, uncommitted/stashed changes.
4. Present briefing:
```
Since last session ({{date}}):
- {{N}} commits, {{N}} files changed
- New: {{files/deps}}
- Drift: {{doc/code mismatches}}
- Next up: {{from TASKS.md}}
```
5. Fix small drift immediately. Flag larger updates for user decision.
