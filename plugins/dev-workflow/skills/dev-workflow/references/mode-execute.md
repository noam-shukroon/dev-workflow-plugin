# Mode: Execute

Run a batch of tasks using agent team orchestration.

## Phase A — Analyze & Plan

1. Read CLAUDE.md + relevant `.claude/` files. Check `.claude/LEARNINGS.md` if it exists — use learnings to inform batch grouping, tool selection, and verification focus.
2. List tasks with target files. Build dependency graph.
3. Group into batches:

**Batch 1 — Independent fixes (parallel):** No file overlap → `superpowers: dispatching-parallel-agents`, one subagent per task.

**Batch 2 — Feature work (sequential):** Shared files/dependencies → `/feature-dev` (code-explorer → code-architect → implement).

**Batch 3 — Cross-cutting concerns:** Many files, same change type → sequential after Batch 1.

4. Write plan using `superpowers: writing-plans`.

## Phase B — Execute batches

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

## Phase C — Simplify

Run `/simplify` on all changed files.

## Phase D — Verify

1. **Build check**: full build + lint + type-check, all must pass.
2. **Visual check** (UI changes only): `claude-in-chrome` → desktop (1440x900) + mobile (390x844). Walk through the verification checklist from the task prompt. Screenshots to `qa-screenshots/`.
3. Fix failures before proceeding. Use `superpowers: verification-before-completion`.

## Phase E — Wrap up

1. Squash `checkpoint:` commits into meaningful commits. Ask user for preferred granularity.
2. `/code-review` on all changes.
3. Update `.claude/CHANGELOG.md` — dated summary entry.
4. Update `.claude/ARCHITECTURE.md` if new files/components added.
5. Update `.claude/TASKS.md` — check off items, update status.
6. `/revise-claude-md`.
7. Append to `.claude/LEARNINGS.md` — categorized actionable rules (see below).
8. **Doc hygiene** — if a major phase just completed or docs feel bloated, run the full periodic hygiene pass from `references/doc-evolution-rules.md` (compress TOOLS/TASKS/CHANGELOG/DECISIONS/LEARNINGS/CONVENTIONS + bloat check).
9. Clean up: `rm -rf "$(echo $TMPDIR)claude-code/"`, remove `qa-screenshots/` (ask user), remove leftover worktrees.

## LEARNINGS.md format

After wrap-up, append to `.claude/LEARNINGS.md`:

```markdown
## Session N — {{summary}}
### Batch planning
- **{{Rule}}** — {{why}}
### Code hygiene
- **{{Rule}}** — {{why}}
### Verification
- **{{Rule}}** — {{why}}
```

Bold rule first, context after. Categorize by concern, not narrative. Do NOT use "What worked" / "What didn't work" sections — extract the adjustment directly. Mark graduated rules (promoted to CLAUDE.md/CONVENTIONS.md) with checkmark. If a learning recurs across 3+ projects, suggest updating this skill.
