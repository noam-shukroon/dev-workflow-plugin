# Mode: Execute

Run a batch of tasks using agent team orchestration.

## Phase A — Analyze & Plan

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
6. **Session scope check.** Split if: 8+ tasks, 3+ batches with visual verification, or 4+ unrelated areas.

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

**Pre-commit gate:** Every checkpoint commit requires build + tests to pass first. Never commit failing code.

**Deployment check:** Before adding any dependency, check CLAUDE.md for deployment constraints (platform, architecture). Warn if a new dependency could cause environment mismatch (e.g., ARM64 binary on x64 deploy target).

**Per task:** change → build/lint/test → checkpoint commit on pass → recovery on fail.

**Agent delegation:** For complex subtasks, delegate to role-based agents:

| Situation | Delegate to |
|-----------|-------------|
| Build/test failure during task | `references/agent-build-resolver.md` |
| Security-sensitive changes (auth, crypto, input handling) | `references/agent-security-reviewer.md` |
| Performance-critical paths (DB queries, hot loops, large data) | `references/agent-performance-optimizer.md` |

Read the agent's reference file, then dispatch as a subagent with the relevant context.

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

1. `/simplify` on changed files — parallel subagents per file group.
2. Build + lint + type-check — all must pass.
3. Visual check (UI only): `claude-in-chrome` → desktop (1440x900) + mobile (390x844). Screenshots to `qa-screenshots/`.
4. Fix failures. Use `superpowers: verification-before-completion`.

## Phase D — Wrap-up

1. Squash `checkpoint:` commits into meaningful commits. Ask user for granularity.
2. `/code-review` on all changes. Additionally dispatch `references/agent-code-reviewer.md` for thorough review.
3. **Parallel subagents** for steps 3a-3e (different files):
   - 3a. Update `.claude/CHANGELOG.md` — dated summary.
   - 3b. Update `.claude/ARCHITECTURE.md` if new files/components.
   - 3c. Update `.claude/TASKS.md` — check off items, update status.
   - 3d. `/revise-claude-md`.
   - 3e. Dispatch `references/agent-doc-updater.md` to sync all `.claude/` docs.
4. Append to `.claude/LEARNINGS.md`: `## Session N — {{summary}}` with categorized rules (batch planning, code hygiene, verification). Bold rule first, context after. No narrative sections. Mark graduated rules ✅.
5. **Doc hygiene** — if major phase completed or docs bloated, run hygiene pass from `references/doc-evolution-rules.md`.
6. Clean up: `rm -rf "$(echo $TMPDIR)claude-code/"`, remove `qa-screenshots/` (ask user), remove leftover worktrees.
