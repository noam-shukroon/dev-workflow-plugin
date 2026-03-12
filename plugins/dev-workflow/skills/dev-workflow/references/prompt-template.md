# Prompt Template — Task Handoff for Claude Code

Copy this template when drafting a new task prompt. Fill in the `{{placeholders}}` and delete sections that don't apply.

---

## Setup

Read `CLAUDE.md` first, then load the `.claude/` files relevant to this task (see the "When to read what" table in CLAUDE.md).

## Tasks

{{TASK_LIST — describe each task with:
- ID (e.g., T1, T2, T3)
- Title
- File(s) involved
- What to do (be specific — include exact values, strings, class names)
- Size: S / M / L}}

## Batching

Analyze file overlap across the tasks above and group into batches:

{{BATCHES — or use this default structure:}}

**Batch 1 — Independent quick fixes (parallel):**
{{List tasks that touch different files with no overlap. Use `superpowers: dispatching-parallel-agents`.}}

**Batch 2 — Feature work (sequential, use /feature-dev):**
{{List tasks that share files or depend on each other. Run code-explorer → code-architect → implement.}}

**Batch 3 — Cross-cutting concerns:**
{{List tasks that span many files (e.g., loading states, formatting, refactoring). Can run after Batch 1.}}

## Agent Instructions

- **Batch 1**: Use `superpowers: dispatching-parallel-agents` — one subagent per fix. Each subagent makes the change and runs the build/lint commands.
- **Batch 2**: Use `/feature-dev` — `code-explorer` traces existing patterns, `code-architect` designs the approach, then implement sequentially. Use `context7` for any library API questions. `frontend-design` is auto-active for UI work.
- **Batch 3**: Implement sequentially, build/lint after each.
- After all batches: run `/simplify` on changed files.

## Verification

1. Run the full build/lint/type-check suite — must pass clean.
2. {{VISUAL_CHECKS — if UI changes, add claude-in-chrome verification:}}
   - Start dev server if not running
   - Use `claude-in-chrome`: `tabs_context_mcp` → `tabs_create_mcp` → navigate to app
   - Desktop (1440×900): {{checklist}}
   - Mobile (390×844): {{checklist}}
   - Save screenshots to `qa-screenshots/`.
3. If any check fails, fix before proceeding.

## Wrap-up

1. `/code-review` on all changes.
2. Update `.claude/CHANGELOG.md` with a summary entry.
3. Update `.claude/ARCHITECTURE.md` if new files/components were added.
4. `/revise-claude-md` to capture learnings.
