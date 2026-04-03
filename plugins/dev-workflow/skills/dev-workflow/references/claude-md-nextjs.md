# Next.js CLAUDE.md Template

Detection: `next.config.*` present in project root.

Fill in `{{placeholders}}`. Delete unused sections.

```markdown
# CLAUDE.md

**{{project_name}}** — {{one-line description}}.
Built with Next.js (App Router), TypeScript, React. Hosted on {{hosting}}.

## Hard Constraints

- **App Router only.** All routes live in `app/`. Never create files in `pages/`.
- **Server Components by default.** Only add `"use client"` when the component needs browser APIs, event handlers, or React hooks (useState, useEffect, etc.).
- **Strict TypeScript.** No `any` types. No `@ts-ignore`. All props and returns typed.
- **Production-ready code only.** No placeholders, no TODO-only implementations. Proper error handling, loading states, and validation.
- {{additional_constraint}}

## North Star Metric

{{the_one_metric_that_matters}}

## Documentation

All detailed docs in `.claude/`. Load only what you need per routing table below.

| File | Contents |
|------|----------|
| `.claude/ARCHITECTURE.md` | Route tree, layouts, API routes, data flow, DB schema |
| `.claude/CONVENTIONS.md` | Component patterns, styling tokens, naming conventions |
| `.claude/DECISIONS.md` | Why choices were made, rejected alternatives |
| `.claude/TASKS.md` | Phase checklist, current work, session handoff |
| `.claude/CHANGELOG.md` | Audit trail of doc updates |

### Context routing

| Task involves | Load |
|---------------|------|
| File structure, routes, layouts, DB, new files | ARCHITECTURE.md |
| Styling, naming, component patterns, types | CONVENTIONS.md |
| Understanding *why* something was built this way | DECISIONS.md |
| Phase progress, what's next, handoff | TASKS.md |

Never load all files speculatively.

### Doc evolution

Update `.claude/` files after tasks that change structure or decisions. Log in CHANGELOG.md.

## Preferences

- Server Components for data fetching. Client Components only for interactivity.
- Co-locate components with their routes when route-specific.
- Shared components go in `components/`. Shared utilities in `lib/`.
- Use `next/image` for all images. Never use raw `<img>` tags.
- Use `next/link` for all internal navigation. Never use raw `<a>` for internal links.
- Use `next/font` for font loading. No external font CDN links.
- Respond concisely. No filler.
- Ask before assuming when decisions could go multiple ways.

## Commands

\```bash
npm run dev        # Dev server (http://localhost:3000)
npm run build      # Production build
npm run lint       # ESLint
npx tsc --noEmit   # Type check
npm test           # Run tests
\```

## Pre-Commit Checks

Always run before committing:
\```bash
npm run build      # Must pass — catches SSR errors, type issues, route problems
npm test           # Must pass
\```
Never commit code that hasn't passed these checks.

## Key Patterns

| Pattern | Convention |
|---------|-----------|
| Data fetching | Server Components with `async`/`await`, no `useEffect` for initial data |
| API routes | Route Handlers in `app/api/` using `route.ts` (GET, POST, etc.) |
| Mutations | Server Actions in `actions/` files with `"use server"` |
| Loading states | `loading.tsx` per route segment, `<Suspense>` for component-level |
| Error handling | `error.tsx` per route segment, `not-found.tsx` for 404s |
| Metadata | Export `metadata` or `generateMetadata` from `page.tsx` / `layout.tsx` |
| Middleware | Single `middleware.ts` at project root for auth, redirects, headers |
| Environment vars | Server-only by default. Prefix with `NEXT_PUBLIC_` only when needed client-side |

## Testing

- **Framework:** Vitest + React Testing Library
- **Unit tests:** Co-located as `*.test.tsx` next to the component
- **Integration tests:** In `__tests__/` directories
- Test Server Components by testing their rendered output
- Test Client Components with user interaction simulation
- Mock `next/navigation`, `next/headers` when needed

## Git Workflow

- Default branch: `{{default_branch}}`
- Conventional commits: `feat:`, `fix:`, `refactor:`, `style:`, `chore:`
- Push to the current branch only — never create new branches without explicit approval.
- Never stage `.env*` files, credentials, or secrets.
- Run `git status` + `git branch` before any git operation.
```
