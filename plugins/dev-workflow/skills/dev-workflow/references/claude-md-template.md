# CLAUDE.md Template

Fill in `{{placeholders}}`. Delete unused sections.

```markdown
# CLAUDE.md

**{{Project Name}}** — {{one-line description}}.
Built with {{tech stack}}. Hosted on {{hosting}}.

## Hard Constraints

- {{e.g., "Do not touch `supabase/migrations/` without explicit reason."}}
- {{e.g., "Do not build features marked Out of Scope."}}
- **Production-ready code only.** No placeholders. Proper error handling, types, validation.

## North Star Metric

{{The one metric that matters.}}

## Documentation

All detailed docs in `.claude/`. Read only what you need — use the mapping below.

| File | Contents |
|------|----------|
| `.claude/ARCHITECTURE.md` | File tree, entry flow, directory layout, DB schema, imports |
| `.claude/CONVENTIONS.md` | Styling tokens, coding conventions, component patterns |
| `.claude/DECISIONS.md` | Why choices were made, rejected alternatives, tradeoffs |
| `.claude/TASKS.md` | Phase checklist, recipes, session handoff |
| `.claude/CHANGELOG.md` | Audit trail of doc updates |
| `.claude/TOOLS.md` | Task → tool mapping, project-specific tool notes |

### When to read what

| Task involves… | Read |
|---|---|
| Components, layout, pages, routes | `ARCHITECTURE.md` |
| Styling, colors, coding patterns | `CONVENTIONS.md` |
| Understanding *why* something was built this way | `DECISIONS.md` |
| Phase progress, recipes, handoff | `TASKS.md` |
| DB schema, API routes | `ARCHITECTURE.md` |
| Which tool for a task | `TOOLS.md` |

Never load all files speculatively.

### Documentation Evolution

- Update after every task that changes structure/conventions/decisions — same session.
- Create new `.claude/` files when needed. Add to both tables.
- Split at ~200 lines. Total under ~1,000.
- Archive shipped decisions. Log every update in CHANGELOG.md.

## Preferences

- Use agent teams for multi-task work.
- Ask before assuming when decisions could go multiple ways.
- {{project-specific preferences}}

## Commands

\```bash
{{dev}}     # Dev server
{{build}}   # Build
{{lint}}    # Lint
{{test}}    # Tests
{{types}}   # Type check
\```

## Git Workflow

- Default branch: `main`
- {{branching strategy}}
- Conventional commits: `feat:`, `fix:`, `refactor:`, `style:`, `chore:`
```
