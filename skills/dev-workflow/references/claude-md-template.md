# CLAUDE.md Template

Copy this template and fill in the `{{placeholders}}` for your project.

---

```markdown
# CLAUDE.md

**{{Project Name}}** — {{One-line description}}.
Built with {{tech stack}}. Hosted on {{hosting}}.
{{Optional: URL where the app lives.}}

## Hard Constraints

- {{Constraint 1 — e.g., "Do not touch `supabase/migrations/` without explicit reason."}}
- {{Constraint 2 — e.g., "Do not build features marked Out of Scope (v1)."}}
- **Follow the phased build plan.** Do not skip ahead unless explicitly asked.
- **Production-ready code only.** No placeholder logic. Use proper error handling, types, and validation.
- {{Design constraint — e.g., "Mobile-first design."}}

## North Star Metric

{{The one metric that matters — e.g., "Appointments booked through the platform per month."}}

## Documentation

All detailed docs live in `.claude/`. Read only what you need per task — use the mapping below.

| File | Contents |
|------|----------|
| `.claude/ARCHITECTURE.md` | File tree, entry flow, directory layout, DB schema, import conventions |
| `.claude/CONVENTIONS.md` | Styling tokens, theme values, coding conventions, component patterns |
| `.claude/DECISIONS.md` | Why certain choices were made, rejected alternatives, known tradeoffs |
| `.claude/TASKS.md` | Phase checklist, common task recipes, session handoff prompt |
| `.claude/CHANGELOG.md` | Audit trail of all documentation updates |
| `.claude/TOOLS.md` | Plugins, MCP servers, skills, agents — when to use which tool |

### When to read what

| If the task involves… | Read |
|---|---|
| Components, layout, new pages, routes | `ARCHITECTURE.md` |
| Styling, colors, coding patterns, types | `CONVENTIONS.md` |
| Understanding *why* something was built a certain way | `DECISIONS.md` |
| Phase progress, routine recipes, session handoff | `TASKS.md` |
| Database schema, API routes | `ARCHITECTURE.md` |
| Which plugin or MCP to use for a task | `TOOLS.md` |
| {{Add project-specific rows as needed}} | |

If a task spans multiple concerns, load the relevant files — but never load all of them speculatively.

### Documentation Evolution Rules

These docs are living documents. Follow these rules:

- **Update after every task** that changes project structure, conventions, or decisions — in the same session.
- **Create new files** if a new major concern emerges that doesn't fit existing files. Add to both tables above.
- **Split files** if any `.claude/` file exceeds ~200 lines.
- **Archive** irrelevant decisions in DECISIONS.md with a date and reason.
- **Changelog**: Every documentation update gets a dated entry in `.claude/CHANGELOG.md`.

## Preferences

- Add annotations when writing/changing code, keep them updated and easy to understand.
- Use agent teams for multi-task work (batch independent tasks in parallel, sequence dependent ones).
- Ask before assuming when a decision could go multiple ways.
- One task at a time within each phase — walk through step by step.
- {{Add project-specific preferences — e.g., "Keep files organized following Next.js App Router conventions."}}

## Commands

```bash
{{dev command}}     # Start dev server
{{build command}}   # Build for production
{{lint command}}    # Lint
{{test command}}    # Run tests (if applicable)
{{typecheck}}       # Type checking (if applicable)
```

## Git Workflow

- Default branch: `main`.
- {{Branching strategy — e.g., "Feature branches per phase (`phase-1/setup`, `phase-2/booking-flow`)."}}
- Commit messages: conventional commits (`feat:`, `fix:`, `refactor:`, `style:`, `chore:`).

## Go-to-Market Context

{{Optional section — business context that affects technical decisions.
E.g., pilot size, pricing, target market, future expansion plans.}}
```
