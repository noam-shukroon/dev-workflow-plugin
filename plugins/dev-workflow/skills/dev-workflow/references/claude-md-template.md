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

All detailed docs in `.claude/`. Load only what you need per routing table below.

| File | Contents |
|------|----------|
| `.claude/ARCHITECTURE.md` | File tree, entry flow, directory layout, DB schema, imports |
| `.claude/CONVENTIONS.md` | Styling tokens, coding conventions, component patterns |
| `.claude/DECISIONS.md` | Why choices were made, rejected alternatives, tradeoffs |
| `.claude/TASKS.md` | Phase checklist, recipes, session handoff |
| `.claude/CHANGELOG.md` | Audit trail of doc updates |
| `.claude/TOOLS.md` | Task → tool mapping, project-specific tool notes |

### Context routing

| Task involves | Load |
|---------------|------|
| File structure, layout, DB, routes, new files | ARCHITECTURE.md |
| Styling, naming, code patterns, types | CONVENTIONS.md |
| Understanding *why* something was built this way | DECISIONS.md |
| Phase progress, recipes, handoff | TASKS.md |
| Which tool for a task | TOOLS.md |

Never load all files speculatively.

### Doc evolution

Update `.claude/` files after tasks that change structure/conventions/decisions. Log in CHANGELOG.md. Full rules in `.claude/` reference docs.

## Preferences

- Use agent teams for multi-task work.
- Respond concisely. No filler, no unnecessary politeness.
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

## Pre-Commit Checks

Always run before committing:
\```bash
{{build}}   # Must pass
{{test}}    # Must pass
\```
Never commit code that hasn't passed these checks.

## Deployment Constraints

- Platform: {{e.g., AWS Amplify, Vercel, Railway}}
- Architecture: {{e.g., x64 — never add ARM-specific dependencies}}
- {{Other constraints — e.g., no native binaries, max bundle size}}

## Git Workflow

- Default branch: `main`
- {{branching strategy}}
- Conventional commits: `feat:`, `fix:`, `refactor:`, `style:`, `chore:`
- Push to the current branch only — never create new branches without explicit approval.
- Never stage `.env*` files, credentials, or secrets.
- Run `git status` + `git branch` before any git operation.
```
