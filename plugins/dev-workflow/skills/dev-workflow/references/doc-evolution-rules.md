# Documentation Evolution Rules

These rules keep the `.claude/` system accurate as a project grows. Apply them after every task — not as a follow-up session.

## When to update existing files

After any task that changes one of these:
- **Project structure** (new files, moved files, deleted files) → update `ARCHITECTURE.md`
- **Coding patterns** (new convention, changed approach) → update `CONVENTIONS.md`
- **Design decisions** (chose a library, rejected an approach) → update `DECISIONS.md`
- **Phase progress** (completed a task, started a new phase) → update `TASKS.md`
- **Tool changes** (installed a plugin, added an MCP server) → update `TOOLS.md`

Every update also gets a dated entry in `CHANGELOG.md`.

## When to create new files

If a new concern emerges that doesn't fit cleanly into any existing file, create a new `.claude/<NAME>.md`. Common triggers:

- API routes get complex → `.claude/API.md`
- Test runner is configured → `.claude/TESTING.md`
- Deployment pipeline needs documentation → `.claude/DEPLOY.md`
- Multiple environments with different configs → `.claude/ENVIRONMENTS.md`

After creating a new file:
1. Add it to CLAUDE.md's documentation table
2. Add it to CLAUDE.md's "When to read what" table
3. Log the creation in CHANGELOG.md

## When to split files

If any `.claude/` file exceeds ~200 lines, split it by sub-concern. Examples:
- `ARCHITECTURE.md` → `ARCHITECTURE.md` (overview) + `SCHEMA.md` (database details)
- `CONVENTIONS.md` → `CONVENTIONS.md` (code) + `DESIGN.md` (visual/styling)

After splitting:
1. Update CLAUDE.md tables to point to the new files
2. Add redirects or notes in the original file
3. Log in CHANGELOG.md

## When to archive

If a decision in `DECISIONS.md` is no longer relevant (feature removed, tradeoff no longer applies):
1. Move the decision to an `## Archived` section at the bottom of `DECISIONS.md`
2. Add a date and one-line reason: `## [Title] (archived YYYY-MM-DD — feature removed)`
3. Log in CHANGELOG.md

## Changelog format

Every CHANGELOG.md entry follows this format:

```markdown
## YYYY-MM-DD (session N — short description)
- **File**: What changed and why.
- **File**: What changed and why.
```

Group related changes under a single date+session header. Be specific about what changed — "Updated ARCHITECTURE.md" is useless. "Added `components/dashboard/time-group.tsx` to ARCHITECTURE.md file tree" is useful.

## README.md maintenance

`README.md` is the public-facing project overview. Update it when:
- A phase is completed (update progress section)
- The stack changes (new dependency, tool, or service)
- Setup instructions change (new env vars, new commands)
- The project structure changes significantly

The README should reflect the current working state, not aspirational features. Mark upcoming features as "planned" with their phase number.
