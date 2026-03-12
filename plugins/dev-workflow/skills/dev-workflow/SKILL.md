---
name: dev-workflow
description: "Project workflow engine: scaffolds a living documentation system (.claude/ files), orchestrates agent teams for task execution, and standardizes verification/wrap-up phases. Use this skill whenever starting a new project (scaffold mode), executing a multi-task prompt (execute mode), handing off between sessions (handoff mode), or when the user mentions 'agent team', 'workflow', 'scaffold docs', 'project setup', or wants to organize work into parallel/sequential batches. Also trigger when the user asks to plan, batch, or coordinate multiple tasks — even if they don't name this skill explicitly."
---

# Dev Workflow

A reusable workflow engine for any project. Three modes:

1. **Scaffold** — set up a new project with living documentation
2. **Execute** — run a batch of tasks using an agent team
3. **Handoff** — prepare a session handoff prompt

Detect which mode from context. If unclear, ask.

---

## Mode 1: Scaffold

Use when starting a new project or adding the documentation system to an existing one.

### Step 1: Create CLAUDE.md

Create a `CLAUDE.md` in the project root. This is the entry point — every session starts by reading it. Include:

- **Project name and one-line description**
- **Hard constraints** (things Claude must never do — e.g., "don't touch migrations", "no new dependencies without asking")
- **North star metric** (the one number that matters)
- **Documentation table** mapping `.claude/` files to their contents
- **"When to read what" table** mapping task types to which files to load
- **Documentation evolution rules** (see `references/doc-evolution-rules.md`)
- **Preferences** (coding style, communication style, tool usage)
- **Commands** (dev server, build, lint, test, type-check)
- **Git workflow** (branching strategy, commit conventions)

Read `references/claude-md-template.md` for the full template.

### Step 2: Create .claude/ files

Create a `.claude/` directory with these files:

| File | Purpose |
|------|---------|
| `ARCHITECTURE.md` | File tree with implementation status, directory layout, DB schema, entry flows, import conventions |
| `CONVENTIONS.md` | Styling tokens, naming conventions, code patterns, state management, error handling, TypeScript rules, localization/i18n |
| `DECISIONS.md` | Why choices were made, rejected alternatives, known tradeoffs. Include an `## Archived` section at the bottom. |
| `TASKS.md` | Phase checklist, current status, session handoff prompt, common task recipes |
| `CHANGELOG.md` | Audit trail — every documentation update gets a dated entry |
| `TOOLS.md` | All installed plugins, skills, MCP servers, agents. Include a "When to Use What" quick-reference table. |

Read `references/claude-docs-scaffold.md` for templates of each file.

### Step 3: Create PROMPT-TEMPLATE.md

Copy `references/prompt-template.md` to the project root. This is the reusable template for writing task prompts.

### Step 4: Verify

- All 6 `.claude/` files exist and are populated
- `CLAUDE.md` references all `.claude/` files in both tables
- `PROMPT-TEMPLATE.md` exists at project root
- Run a quick sanity check: does the "When to read what" table cover the common task types for this project?

---

## Mode 2: Execute

Use when given a batch of tasks to implement. This is the agent team orchestration pattern.

### Phase A — Analyze & Plan

1. Read `CLAUDE.md` and the relevant `.claude/` files (use the "When to read what" table).
2. List all tasks with their target files.
3. Build a dependency graph — which tasks touch the same files?
4. Group into batches:

**Batch 1 — Independent fixes (parallel):**
Tasks that touch different files with zero overlap. These are safe to run simultaneously.
→ Use `superpowers: dispatching-parallel-agents` — one subagent per task.

**Batch 2 — Feature work (sequential):**
Tasks that share files or have logical dependencies. Must run in order.
→ Use `/feature-dev` for multi-file features:
  - `code-explorer` — trace existing patterns and data flow
  - `code-architect` — design the approach before coding
  - Implement one feature at a time

**Batch 3 — Cross-cutting concerns:**
Tasks that touch many files with the same change (formatting, loading states, refactoring).
→ Run sequentially after Batch 1 (to avoid merge conflicts with Batch 2).

5. Write the plan using `superpowers: writing-plans`. Present batches clearly.

### Phase B — Execute batches

Run batches in order (1 → 2 → 3, though 1 and 2 can overlap if no file conflicts).

**For every task:**
- Make the change
- Run the project's build/lint/test commands
- If it fails, fix before moving on

**Tool selection per task type:**

| Task type | Tool |
|-----------|------|
| Independent small fix | `superpowers: dispatching-parallel-agents` |
| Multi-file feature | `/feature-dev` |
| UI/styling work | `frontend-design` (auto-active) |
| Library API lookup | `context7` ("use context7") |
| Bug investigation | `superpowers: systematic-debugging` |

### Phase C — Simplify

After all batches complete, run `/simplify` on all changed files. The code-simplifier will:
- Reduce unnecessary complexity/nesting
- Consolidate related logic
- Improve naming consistency
- Eliminate redundant code

### Phase D — Verify

1. **Build check**: Run the project's full verification commands (build + lint + type-check). All must pass clean.

2. **Visual check** (if UI was changed): Use `claude-in-chrome`:
   - `tabs_context_mcp` → `tabs_create_mcp` → navigate to the app
   - Test at desktop viewport (1440×900) and mobile viewport (390×844)
   - Walk through the verification checklist from the task prompt
   - Take screenshots, save to `qa-screenshots/`

3. **If anything fails**, fix it before proceeding. Use `superpowers: verification-before-completion` — evidence before assertions.

### Phase E — Wrap up

1. `/code-review` — final quality pass on all changes.
2. Update `.claude/CHANGELOG.md` — one dated entry summarizing what changed.
3. Update `.claude/ARCHITECTURE.md` — if new files/components were added.
4. Update `.claude/TASKS.md` — check off completed items, update current status.
5. `/revise-claude-md` — capture any learnings in project memory.
6. Create or append to `.claude/LEARNINGS.md` — capture what worked and what didn't (see Skill Evolution below).
7. **Clean up Claude Code's temporary directory** — remove the temp directory used during the session to avoid stale files accumulating:
   ```bash
   rm -rf "$(echo $TMPDIR)claude-code/"
   ```
   Also remove any workflow-generated temporary artifacts:
   - `qa-screenshots/` — delete after confirming screenshots are no longer needed (ask user if unsure)
   - Any leftover git worktree directories from parallel agent execution

---

## Mode 3: Handoff

Use when preparing for a session break or context handoff.

1. Read `.claude/TASKS.md` to get the current status.
2. Generate a handoff prompt:

```
Read CLAUDE.md first — it contains hard constraints and a documentation map.
Then read only the .claude/ files relevant to your task (see "When to read what" table).

Current status: {{what was last completed}}
Next up: {{what needs to happen next}}
Context: {{any decisions made this session that aren't in docs yet}}
```

3. Update `.claude/TASKS.md` with the latest status.
4. Update `.claude/CHANGELOG.md` if any doc changes were made.

---

## Documentation Evolution Rules

These rules keep the `.claude/` system accurate as the project grows. They apply after every task.

- **When to update**: After any task that changes project structure, conventions, or decisions — in the same session, not as a follow-up.
- **When to create new files**: If a new major concern emerges that doesn't fit existing files (e.g., `.claude/API.md`, `.claude/TESTING.md`). Add to CLAUDE.md's documentation table.
- **When to split files**: If any `.claude/` file exceeds ~200 lines, split by sub-concern.
- **When to archive**: If a decision is no longer relevant, move to `## Archived` in DECISIONS.md with a date and reason.
- **Changelog**: Every update gets a dated entry in CHANGELOG.md.

---

## Skill Evolution

The skill improves with use. After every Execute run, capture what worked and what didn't so future runs benefit.

### Per-project learnings file

After Phase E (wrap-up), create or append to `.claude/LEARNINGS.md` in the project:

```markdown
## YYYY-MM-DD — {{task summary}}

### What worked
- {{e.g., "Parallel batch of 7 independent fixes completed with zero conflicts"}}
- {{e.g., "feature-dev explorer step caught a shared utility that avoided duplication"}}

### What didn't work
- {{e.g., "Batch 2 and 3 had an unexpected file overlap in utils.ts — should have been in same batch"}}
- {{e.g., "/simplify removed a comment that was actually important context"}}

### Adjustments for next time
- {{e.g., "Always check lib/utils.ts for cross-batch overlap before dispatching"}}
- {{e.g., "Add 'preserve annotation comments' rule to simplify pass"}}
```

### Reading learnings

At the start of every Execute run (Phase A), check if `.claude/LEARNINGS.md` exists. If it does, read it before planning batches. Use past adjustments to inform:
- Batch grouping (avoid past file-overlap mistakes)
- Tool selection (if a tool caused issues, try alternatives)
- Verification focus (if past runs missed things, add those checks)

### Skill-level patterns

If the same learning appears across 3+ projects, it's a pattern worth baking into the skill itself. Flag it to the user: "This adjustment has come up in multiple projects — want me to update the dev-workflow skill to include it by default?"

The user can then use `/skill-creator` to update the global skill with the new pattern.

---

## Reference Files

Read these when you need detailed templates or examples:

| File | When to read |
|------|-------------|
| `references/claude-md-template.md` | Scaffolding a new CLAUDE.md |
| `references/claude-docs-scaffold.md` | Creating `.claude/` files from scratch |
| `references/prompt-template.md` | Writing a task prompt for agent team execution |
| `references/doc-evolution-rules.md` | Detailed rules for keeping docs current |
| `.claude/LEARNINGS.md` | Per-project learnings from past Execute runs — read at start of Phase A |
