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

**After every `.claude/` file update**, do a quick bloat check:
1. Count the `##` headings — if a file has 5+ top-level sections covering unrelated concerns, it's doing too much.
2. Check if you'd need to read past unrelated content to find what you need — that's a sign the file mixes concerns.
3. If either is true → split by sub-concern.

Examples:
- `ARCHITECTURE.md` has schema, imports, entry flows, and deployment topology → split `SCHEMA.md` and `DEPLOY.md` out
- `CONVENTIONS.md` has styling, code patterns, API contracts, and error handling → split `DESIGN.md` or `API.md` out

After splitting: update CLAUDE.md tables, add redirect note in original, log in CHANGELOG.

## When to archive

Decision no longer relevant → move to `## Archived` in DECISIONS.md with date and reason. Log in CHANGELOG.

## Changelog format

```markdown
## YYYY-MM-DD (session N — short description)
- **File**: What changed and why. (Be specific, not "Updated X".)
```

## Periodic hygiene

Run every 5–10 sessions, or when docs feel bloated:

1. **TOOLS.md** — quick-reference table + project notes only
2. **TASKS.md** — collapse completed phases to summary table
3. **CHANGELOG.md** — archive old entries in `<details>`
4. **DECISIONS.md** — archive shipped decisions as one-liners
5. **LEARNINGS.md** — categorized rules only, mark graduated items ✅
6. **CONVENTIONS.md** — deduplicate across sections
7. **All files** — run the bloat check (5+ unrelated `##` sections → split)
