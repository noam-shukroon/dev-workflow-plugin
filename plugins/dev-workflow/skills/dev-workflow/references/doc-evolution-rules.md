# Documentation Evolution Rules

Apply after every task — same session, not follow-up.

## When to update

| Change | Update |
|--------|--------|
| Project structure (new/moved/deleted files) | `ARCHITECTURE.md` |
| Coding patterns (new convention) | `CONVENTIONS.md` |
| Design decisions (chose/rejected approach) | `DECISIONS.md` |
| Phase progress (completed/started task) | `TASKS.md` |
| Tool changes (installed plugin/MCP) | `TOOLS.md` |

Every update also gets a dated CHANGELOG.md entry.

## When to create new files

If a concern doesn't fit existing files → create `.claude/<NAME>.md`. Examples: `API.md`, `TESTING.md`, `DEPLOY.md`, `ENVIRONMENTS.md`.

After creating: add to CLAUDE.md documentation table + "when to read what" table + CHANGELOG.md.

## When to split

Any file >~200 lines → split by sub-concern. Examples:
- `ARCHITECTURE.md` → + `SCHEMA.md`
- `CONVENTIONS.md` → + `DESIGN.md`

After splitting: update CLAUDE.md tables, add redirect note in original, log in CHANGELOG.

## When to archive

Decision no longer relevant → move to `## Archived` in DECISIONS.md with date and reason. Log in CHANGELOG.

## Changelog format

```markdown
## YYYY-MM-DD (session N — short description)
- **File**: What changed and why. (Be specific, not "Updated X".)
```

## Periodic hygiene

Every 5–10 sessions, or when total `.claude/` lines >~1,000:

1. **TOOLS.md** — quick-reference table + project notes only
2. **TASKS.md** — collapse completed phases to summary table
3. **CHANGELOG.md** — archive old entries in `<details>`
4. **DECISIONS.md** — archive shipped decisions as one-liners
5. **LEARNINGS.md** — categorized rules only, mark graduated items ✅
6. **CONVENTIONS.md** — deduplicate across sections
7. All files under ~200 lines each, total under ~1,000
