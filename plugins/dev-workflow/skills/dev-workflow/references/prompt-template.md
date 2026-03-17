# Prompt Template — Task Handoff

Fill in `{{placeholders}}`, delete unused sections.

## Setup

Read `CLAUDE.md` first, then load relevant `.claude/` files per "When to read what" table.

## Tasks

{{List each task:
- **ID**: T1, T2, ...
- **Title**: what to do
- **Files**: affected files
- **Details**: exact values, class names, specifics
- **Size**: S / M / L}}

## Batching

Analyze file overlap, group into batches:

**Batch 1 — Independent fixes (parallel):**
{{No file overlap → `superpowers: dispatching-parallel-agents`.}}

**Batch 2 — Feature work (sequential, /feature-dev):**
{{Shared files/dependencies → code-explorer → code-architect → implement.}}

**Batch 3 — Cross-cutting:**
{{Many files, same change → sequential after Batch 1.}}

## Agent Instructions

- **Batch 1**: parallel subagents, each runs build/lint after change
- **Batch 2**: `/feature-dev` sequential, `context7` for API lookups, `frontend-design` auto-active for UI
- **Batch 3**: sequential, build/lint after each
- **After all**: `/simplify` on changed files

## Verification

1. Full build + lint + type-check — must pass.
2. Visual (UI changes): `claude-in-chrome` → desktop 1440x900 + mobile 390x844 → screenshots to `qa-screenshots/`.
3. Fix failures before proceeding.

## Wrap-up

1. `/code-review` all changes
2. Update CHANGELOG.md, ARCHITECTURE.md (if new files), `/revise-claude-md`
