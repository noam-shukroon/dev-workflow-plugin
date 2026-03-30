# Documentation Evolution Rules

Apply after every task — same session, not follow-up.

## When to update

| Change | Update |
|--------|--------|
| New/moved/deleted files | `ARCHITECTURE.md` |
| New coding pattern/convention | `CONVENTIONS.md` |
| Chose or rejected an approach | `DECISIONS.md` |
| Completed or started a task | `TASKS.md` |
| Installed plugin/MCP/tool | `TOOLS.md` |

Every update also gets a dated CHANGELOG.md entry.

## When to create new files

Concern doesn't fit existing files → create `.claude/<NAME>.md` (e.g., `API.md`, `TESTING.md`, `DEPLOY.md`). After creating: add to CLAUDE.md tables + CHANGELOG.md.

## When to split

After every `.claude/` file update, quick check:
- 5+ unrelated `##` sections → split by sub-concern
- Must read past unrelated content to find what you need → split

After splitting: update CLAUDE.md tables, add redirect note in original, log in CHANGELOG.

## When to archive

Decision no longer relevant → move to `## Archived` in DECISIONS.md with date and reason. Log in CHANGELOG.

## Changelog format

```
## YYYY-MM-DD (session N — short description)
- **File**: What changed and why. (Specific, not "Updated X".)
```

## Periodic hygiene

Every 5-10 sessions or when docs feel bloated:

| File | Action |
|------|--------|
| TOOLS.md | Quick-reference table + project notes only |
| TASKS.md | Collapse completed phases to summary table |
| CHANGELOG.md | Archive old entries in `<details>` |
| DECISIONS.md | Archive shipped decisions as one-liners |
| LEARNINGS.md | Categorized rules only, mark graduated ✅ |
| CONVENTIONS.md | Deduplicate across sections |
| All files | Bloat check (5+ unrelated `##` → split) |
