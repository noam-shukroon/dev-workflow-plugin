---
name: dev-workflow
description: "Project workflow engine: scaffolds .claude/ docs, orchestrates agent teams, standardizes verification, supports session resume and doc audits. Trigger on: scaffold, execute, handoff, resume, audit, agent team, workflow, batch, project setup, or multi-task coordination."
---

# Dev Workflow

Five modes — detect from context, ask if unclear:

1. **Scaffold** — set up living documentation
2. **Execute** — batch tasks with agent teams
3. **Handoff** — prepare session handoff
4. **Resume** — catch up after a break
5. **Audit** — check documentation health

---

## Mode 1: Scaffold

### Step 0: Detect project context

Introspect before creating files:

1. **Project type** — check for `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `Gemfile`, `pom.xml`, etc.
2. **Commands** — extract build/test/lint from manifest. Pre-fill CLAUDE.md Commands section.
3. **Framework** — detect indicators (Next.js, FastAPI, Rails, etc.). Tailor CONVENTIONS.md — skip irrelevant sections.
4. **Monorepo** — check for `workspaces`, `lerna.json`, `nx.json`. Note structure in ARCHITECTURE.md.
5. **Existing docs** — check for README.md, CONTRIBUTING.md, existing .claude/. Incorporate, don't duplicate.

### Scaffold size

| Size | Files | Best for |
|------|-------|----------|
| **Minimal** | CLAUDE.md + ARCHITECTURE.md + TASKS.md | Small projects, CLIs, libraries (<10 source files) |
| **Standard** | All 6 .claude/ files + CLAUDE.md | Most projects (default) |
| **Full** | Standard + PROMPT-TEMPLATE.md | Multi-person/agent team workflows |

### Step 1: Create CLAUDE.md

Read `references/claude-md-template.md` for the template. Include: project name, hard constraints, north star metric, documentation table, "when to read what" table, doc evolution rules (from `references/doc-evolution-rules.md`), preferences, commands, git workflow.

### Step 2: Create .claude/ files

Read `references/claude-docs-scaffold.md` for templates.

| File | Purpose |
|------|---------|
| `ARCHITECTURE.md` | File tree, directory layout, DB schema, entry flows, imports |
| `CONVENTIONS.md` | Styling, naming, code patterns, state, errors, types, i18n |
| `DECISIONS.md` | Choices made, rejected alternatives, tradeoffs. `## Archived` at bottom |
| `TASKS.md` | Phase checklist, status, handoff prompt, recipes |
| `CHANGELOG.md` | Dated audit trail of doc updates |
| `TOOLS.md` | Quick-reference table mapping tasks → tools. No tool descriptions (system loads those) |

### Step 3: Create PROMPT-TEMPLATE.md (full scaffold only)

Copy `references/prompt-template.md` to project root.

### Step 4: Verify

- All `.claude/` files for chosen size exist and are populated
- CLAUDE.md references exactly the created files (no dangling refs)
- Auto-detected commands actually work (run them)
- "When to read what" table covers common task types

---

## Mode 2: Execute

### Phase A — Analyze & Plan

1. Read CLAUDE.md + relevant `.claude/` files. Check `.claude/LEARNINGS.md` if it exists.
2. List tasks with target files. Build dependency graph.
3. Group into batches:

**Batch 1 — Independent fixes (parallel):** No file overlap → `superpowers: dispatching-parallel-agents`, one subagent per task.

**Batch 2 — Feature work (sequential):** Shared files/dependencies → `/feature-dev` (code-explorer → code-architect → implement).

**Batch 3 — Cross-cutting concerns:** Many files, same change type → sequential after Batch 1.

4. Write plan using `superpowers: writing-plans`.

### Phase B — Execute batches

Run in order (1 → 2 → 3; 1 and 2 can overlap if no file conflicts).

**Per task:** make change → build/lint/test → checkpoint commit on pass → recovery tree on fail.

**Checkpoints:** `checkpoint: <batch>/<task> — <description>`. Lightweight commits on current branch. Squashed at wrap-up.

**Recovery (on failure):**
1. **Quick fix** (<5 min) → fix, verify, checkpoint, continue
2. **Isolated** (doesn't block others) → `git stash`, log as skipped in TASKS.md, continue, revisit later
3. **Cascading** (blocks downstream) → `git reset --soft` to last checkpoint, re-plan with failure as constraint, update DECISIONS.md
4. **Wrong approach** → `git reset --soft` to batch start, ask user before continuing

**Progress:** Between batches (and every 3+ tasks), print status: `--- Progress: 4/7 tasks | Batch 2 | 1 skipped ---`. Update TASKS.md in real-time.

**Tool selection:**

| Task type | Tool |
|-----------|------|
| Independent fix | `superpowers: dispatching-parallel-agents` |
| Multi-file feature | `/feature-dev` |
| UI/styling | `frontend-design` (auto-active) |
| Library API | `context7` |
| Bug | `superpowers: systematic-debugging` |

### Phase C — Simplify

Run `/simplify` on all changed files.

### Phase D — Verify

1. **Build check**: full build + lint + type-check, all must pass.
2. **Visual check** (UI changes only): `claude-in-chrome` → desktop (1440x900) + mobile (390x844) → screenshots to `qa-screenshots/`.
3. Fix failures before proceeding. Use `superpowers: verification-before-completion`.

### Phase E — Wrap up

1. Squash `checkpoint:` commits into meaningful commits. Ask user for preferred granularity.
2. `/code-review` on all changes.
3. Update `.claude/CHANGELOG.md` — dated summary entry.
4. Update `.claude/ARCHITECTURE.md` if new files/components added.
5. Update `.claude/TASKS.md` — check off items, update status.
6. `/revise-claude-md`.
7. Append to `.claude/LEARNINGS.md` (see Skill Evolution below).
8. Doc hygiene check if total `.claude/` lines >1000 or major phase completed (see `references/doc-evolution-rules.md`).
9. Clean up: `rm -rf "$(echo $TMPDIR)claude-code/"`, remove `qa-screenshots/` (ask user), remove leftover worktrees.

---

## Mode 3: Handoff

1. Read `.claude/TASKS.md` for current status.
2. Run `git status` and `git diff --stat` for uncommitted work.
3. Generate handoff prompt:
```
Read CLAUDE.md first, then relevant .claude/ files (see "When to read what" table).
Current status: {{last completed}}
Next up: {{next tasks}}
Blockers: {{unresolved decisions}}
Uncommitted: {{modified files or "none"}}
Context: {{session decisions not yet in docs}}
```
4. Update TASKS.md with latest status.
5. Update CHANGELOG.md if doc changes were made.

---

## Mode 4: Resume

1. Read `.claude/TASKS.md` for last status and next steps.
2. `git log` since last CHANGELOG.md entry date. Summarize: commits, new branches/PRs, new/deleted files.
3. Check for drift: ARCHITECTURE.md vs actual file tree, new deps not in docs, uncommitted/stashed changes.
4. Present briefing:
```
Since last session ({{date}}):
- {{N}} commits, {{N}} files changed
- New: {{files/deps}}
- Drift: {{doc/code mismatches}}
- Next up: {{from TASKS.md}}
```
5. Fix small drift immediately. Flag larger updates for user decision.

---

## Mode 5: Audit

1. **Inventory** — list `.claude/` files with line counts and last-modified dates.
2. **Cross-refs** — every file in CLAUDE.md tables exists; every `.claude/` file is referenced; "when to read what" covers common tasks.
3. **Staleness** — ARCHITECTURE.md vs actual file tree; TASKS.md "in progress" items that are done; DECISIONS.md active items already shipped.
4. **Line budget** — flag files >200 lines; report total.
5. **Quality** — irrelevant CONVENTIONS.md sections for project type; old CHANGELOG entries to archive; unmarked graduated LEARNINGS.
6. **Report**:
```
Doc Health: 6/6 files | 847/1000 lines
Cross-refs: OK
Stale: ARCHITECTURE.md missing 3 files
Budget: CONVENTIONS.md at 210 lines
Actions: [list fixes needed]
```
7. Apply small fixes (<5) directly. Present larger lists for user decision.

---

## Doc Evolution Rules

Full rules in `references/doc-evolution-rules.md`. Summary:

- Update after any task that changes structure, conventions, or decisions — same session.
- Create new `.claude/` files when a concern doesn't fit existing ones. Add to CLAUDE.md tables.
- Split at ~200 lines. Total under ~1000.
- Archive shipped decisions as one-liners in DECISIONS.md `## Archived`.
- Every update gets a CHANGELOG.md entry.

---

## Skill Evolution

After Execute Phase E, append to `.claude/LEARNINGS.md`:

```markdown
## Session N — {{summary}}
### Batch planning
- **{{Rule}}** — {{why}}
### Code hygiene
- **{{Rule}}** — {{why}}
### Verification
- **{{Rule}}** — {{why}}
```

Format: bold rule first, context after. Categorize by concern, not narrative. Mark graduated rules (promoted to CLAUDE.md/CONVENTIONS.md) with checkmark. At Phase A, read LEARNINGS.md before planning. If a learning recurs across 3+ projects, suggest updating this skill.

---

## Reference Files

| File | When to read |
|------|-------------|
| `references/claude-md-template.md` | Scaffolding CLAUDE.md |
| `references/claude-docs-scaffold.md` | Creating .claude/ files |
| `references/prompt-template.md` | Writing task prompts |
| `references/doc-evolution-rules.md` | Detailed doc evolution + hygiene rules |
| `.claude/LEARNINGS.md` | Start of Execute Phase A |
