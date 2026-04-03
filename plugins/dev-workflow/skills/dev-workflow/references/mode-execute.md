# Mode: Execute

Run a batch of tasks using agent team orchestration. Respect task sizing from SKILL.md — skip phases that don't apply.

## Phase A — Analyze & Plan

_Skip for 1-task / ≤3-file work. Execute directly with build/test gate._

1. Load context per routing table in SKILL.md. Check `.claude/LEARNINGS.md` if it exists.
2. **Read PRD if present.** If `.claude/PRD.md` exists, use it to inform planning: validate tasks against requirements, flag gaps, ensure success metrics are addressable.
3. List tasks with target files. Build dependency graph.
4. **Generate implementation plan** with:
   - Task ordering based on dependencies
   - Risk assessment per task (low/medium/high based on file count, complexity, cross-cutting concerns)
   - Estimated batch groupings
   - Present plan to user for approval before executing.
5. Group into batches:
   - **Batch 1 (parallel):** No file overlap → `superpowers: dispatching-parallel-agents`, one subagent per task.
   - **Batch 2 (sequential):** Shared files/dependencies → `/feature-dev` (code-explorer → code-architect → implement).
   - **Batch 3 (cross-cutting):** Many files, same change type → sequential after Batch 1.
6. **Session scope check** (5+ tasks only). Split if: 8+ tasks, 3+ batches with visual verification, or 4+ unrelated areas.

   If splitting:
   - Group batches into sessions (max ~5 tasks each).
   - Present session plan to user for approval.
   - Orchestrate automatically:
     - Independent sessions → parallel background subagents (`Agent` tool, `run_in_background: true`).
     - Dependent sessions → sequential subagents. Parent waits, checks result, launches next.
     - Each subagent gets: batch assignments, relevant `.claude/` context per routing table, verification instructions.
   - Monitor: success → mark done, continue. Failure → present to user, ask retry/skip/abort.
   - After all sessions → run Phase D (wrap-up) once from parent.

7. Execute on approval.

## Phase B — Execute batches

Run in order (1 → 2 → 3; overlap 1+2 if no file conflicts).

**Pre-batch check:** `git fetch && git log HEAD..origin/$(git branch --show-current) --oneline`. If behind remote: warn user, suggest rebase before executing.

**Pre-commit gate:** Every checkpoint commit requires build + tests to pass first. Never commit failing code.

**Deployment check:** Before adding any dependency, check CLAUDE.md for deployment constraints (platform, architecture). Warn if a new dependency could cause environment mismatch.

**Per task:** change → build/lint/test → quick security scan → checkpoint commit on pass → recovery on fail.

**Security scan (per task):** After build/test pass, grep changed files for: hardcoded secrets (API keys, passwords, tokens), `eval()`, `innerHTML`, raw SQL string concatenation, `.env` references not in `.gitignore`. Flag immediately — don't defer to ship time.

**Agent delegation:** For complex subtasks, read `references/agents.md` and dispatch the relevant role as a subagent:

| Situation | Agent |
|-----------|-------|
| Build/test failure during task | Build Resolver |
| Security-sensitive changes (auth, crypto, input handling) | Security Reviewer |
| Performance-critical paths (DB queries, hot loops, large data) | Performance Optimizer |

**Checkpoints:** `checkpoint: <batch>/<task> — <description>`. Squashed at wrap-up.

**Recovery:**

| Failure | Action |
|---------|--------|
| Quick fix (<5 min) | Fix → verify → checkpoint → continue |
| Isolated | `git stash` → log skip in TASKS.md → continue → revisit |
| Cascading | `git reset --soft` to checkpoint → re-plan → update DECISIONS.md |
| Wrong approach | `git reset --soft` to batch start → ask user |
| Wrong branch | `git stash` → switch to correct branch → `git stash pop` → continue |

**Progress:** Every 3+ tasks: `--- Progress: 4/7 tasks | Batch 2 | 1 skipped ---`. Update TASKS.md live.

**Tool selection:**

| Task type | Tool |
|-----------|------|
| Independent fix | `superpowers: dispatching-parallel-agents` |
| Multi-file feature | `/feature-dev` |
| UI/styling | `frontend-design` (auto-active) |
| Library API | `context7` |
| Bug | `superpowers: systematic-debugging` |

## Phase C — Verify

Scale verification to match the work:

1. Build + lint + type-check + test (always).
2. `/simplify` on changed files — only if >100 lines changed or refactoring tasks.
3. Visual check (UI changes only): `claude-in-chrome` → desktop (1440x900) + mobile (390x844). Screenshots to `qa-screenshots/`.
4. Fix failures. Use `superpowers: verification-before-completion`.

## Phase D — Wrap-up

Scale to task size:

**Small (1-2 tasks, ≤5 files):** Update `.claude/TASKS.md` only. Skip other docs.

**Medium (3-5 tasks):**
1. Squash `checkpoint:` commits into meaningful commits. Ask user for granularity.
2. `/code-review` on all changes.
3. Update `.claude/TASKS.md` and `.claude/CHANGELOG.md`.

**Large (5+ tasks):**
1. Squash `checkpoint:` commits into meaningful commits. Ask user for granularity.
2. `/code-review` on all changes. Additionally dispatch Code Reviewer from `references/agents.md`.
3. **Parallel subagents** for steps 3a-3e (different files):
   - 3a. Update `.claude/CHANGELOG.md` — dated summary.
   - 3b. Update `.claude/ARCHITECTURE.md` if new files/components.
   - 3c. Update `.claude/TASKS.md` — check off items, update status.
   - 3d. `/revise-claude-md`.
   - 3e. Dispatch Doc Updater from `references/agents.md` to sync all `.claude/` docs.
4. Append to `.claude/LEARNINGS.md`: `## Session N — {{summary}}` with categorized rules (batch planning, code hygiene, verification). Bold rule first, context after. No narrative sections. Mark graduated rules ✅.
5. **Doc hygiene** — if major phase completed or docs bloated, run hygiene pass from `references/doc-evolution-rules.md`.
6. Clean up: `rm -rf "$(echo $TMPDIR)claude-code/"`, remove `qa-screenshots/` (ask user), remove leftover worktrees.
