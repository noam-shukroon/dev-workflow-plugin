---
name: audit
description: "Structured project audit with parallel agents, numbered findings, and approval gate. Use when the user asks to audit, review, scan, or check project health — code quality, security, accessibility, docs staleness, or build issues."
---

# Audit

Structured project audit with parallel agents, numbered findings, and approval gate before fixes.

## Phase 1 — Scan (parallel agents)

Spawn one subagent per category using `superpowers: dispatching-parallel-agents`:

| ID | Category | What to check |
|----|----------|---------------|
| C1 | **Build & Types** | `npm run build`, type errors, unused imports, dead code |
| C2 | **Code Quality** | Duplicated logic, overly complex functions, missing error handling at boundaries |
| C3 | **Docs Staleness** | ARCHITECTURE.md vs actual file tree, stale TASKS.md items, outdated DECISIONS.md |
| C4 | **Accessibility** | Missing alt text, ARIA labels, keyboard navigation, color contrast |
| C5 | **Security** | Exposed secrets, XSS vectors, unvalidated inputs, dependency vulnerabilities |

Each agent returns findings in this format:
```
[C1-F1] P1 — Title
Description of the issue.
File: path/to/file.ts:42
```

Severity levels:
- **P0** — Blocks build/deploy or is a security vulnerability
- **P1** — Bug or significant quality issue
- **P2** — Minor improvement or style issue

## Phase 2 — Report

Merge all agent results into a single numbered report, grouped by category:

```
## Audit Report

### C1 · Build & Types (3 findings)
[C1-F1] P0 — Build fails due to missing import
[C1-F2] P1 — Unused variable in auth.ts
[C1-F3] P2 — Implicit any in utils.ts

### C2 · Code Quality (2 findings)
[C2-F1] P1 — Duplicated validation logic in form handlers
[C2-F2] P2 — Function exceeds 80 lines

... etc

Summary: X findings (Y P0, Z P1, W P2)
```

**Do not fix anything yet.** Present the report and wait for user approval.

## Phase 3 — Approval Gate

Ask the user:
- Fix all? Fix by severity (e.g., P0+P1 only)? Fix specific items by ID?
- Any findings to skip or defer?

Only proceed with explicitly approved findings.

## Phase 4 — Fix (parallel agents)

Spawn parallel fix agents — one per approved finding (or group related findings in the same file).

Each fix agent must:
1. Apply the fix
2. Run build + tests to verify no regression
3. Report success or failure

If a fix breaks the build, revert it and report as `fix-failed`.

## Phase 5 — Verify & Commit

1. Run full build + test suite.
2. If passing, commit with message: `fix: audit fixes — [list of finding IDs]`
3. If any test fails, isolate the failing fix and revert it.
4. Report summary:
```
Audit complete:
- Fixed: [C1-F1], [C2-F1], ...
- Skipped: [C1-F3] (user deferred)
- Failed: [C2-F2] (fix broke tests, reverted)
```
