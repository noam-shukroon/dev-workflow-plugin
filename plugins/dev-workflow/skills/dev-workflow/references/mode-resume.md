# Mode: Resume

Return to a project after a break. Run steps 1-3 as **parallel subagents**.

1. Read `.claude/TASKS.md` for last status and next steps.
2. `git log` since last CHANGELOG.md entry date. Summarize: commits, branches/PRs, new/deleted files.
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
