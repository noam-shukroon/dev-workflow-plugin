---
name: dev-workflow
description: "Project workflow engine: scaffolds .claude/ docs, orchestrates agent teams for batch task execution, and standardizes verification/wrap-up phases. Trigger on: scaffold, execute, handoff, resume, audit, agent team, workflow, batch, project setup, or multi-task coordination. Also trigger when the user asks to plan, batch, or coordinate multiple tasks — even if they don't name this skill explicitly."
---

# Dev Workflow

## Defaults

- **Tone**: Concise. No filler, no unnecessary politeness. Cut wasted tokens.
- **Parallelism**: Use agent teams (`superpowers: dispatching-parallel-agents`) for 2+ independent subtasks. Parallel over sequential, always.
- **Git safety** (always active):
  - Before any git operation: run `git status` + `git branch` to confirm state.
  - Never create new branches without explicit user approval.
  - Never stage `.env*` files, credentials, or secrets.
  - Push to the current branch only — never switch branches silently.
  - Never force push.
- **Pre-commit verification**: Run the project's build and test commands before committing. Never commit code that hasn't passed both.
- **Deployment awareness**: Check CLAUDE.md for deployment constraints before adding dependencies or platform-specific code. Warn if a change could break the deploy target.
- **Context loading**: Read CLAUDE.md first. Then load ONLY the `.claude/` files needed:

| Task involves | Load |
|---------------|------|
| File structure, layout, DB, routes, new files | ARCHITECTURE.md |
| Styling, naming, code patterns, types | CONVENTIONS.md |
| Why something was built a certain way | DECISIONS.md |
| Phase progress, what's next | TASKS.md |
| Tool usage, MCP setup | TOOLS.md |

Skip files not relevant to the current work. Re-evaluate between batches.

## Modes

Detect from context. If unclear, ask.

| Mode | Use when | Read |
|------|----------|------|
| **Scaffold** | Starting a project or adding .claude/ docs | `references/mode-scaffold.md` |
| **Execute** | Given a batch of tasks to implement | `references/mode-execute.md` |
| **Handoff** | Preparing for a session break | See below |
| **Resume** | Returning to a project after a break | See below |
| **Audit** | Checking documentation health | `references/mode-audit.md` |
| **Upgrade** | Upgrading .claude/ docs to latest plugin templates | `references/mode-upgrade.md` |

After detecting the mode, read **only** that mode's reference file (for Scaffold/Execute/Audit/Upgrade), then follow its instructions.

## Mode: Handoff

1. Read `.claude/TASKS.md` for current status.
2. Run `git status` and `git diff --stat` for uncommitted work.
3. Generate handoff prompt:
```
Read CLAUDE.md first, then relevant .claude/ files per routing table above.
Current status: {{last completed}}
Next up: {{next tasks}}
Blockers: {{unresolved decisions}}
Uncommitted: {{modified files or "none"}}
Context: {{session decisions not yet in docs}}
```
4. Update TASKS.md with latest status.
5. Update CHANGELOG.md if doc changes were made.

## Mode: Resume

1. Read `.claude/TASKS.md` for last status and next steps.
2. `git log` since last CHANGELOG.md entry date. Summarize: commits, branches/PRs, new/deleted files.
3. Check for drift: ARCHITECTURE.md vs actual file tree, new deps not in docs, uncommitted/stashed changes.

Run steps 1-3 as parallel subagents.

4. Present briefing:
```
Since last session ({{date}}):
- {{N}} commits, {{N}} files changed
- New: {{files/deps}}
- Drift: {{doc/code mismatches}}
- Next up: {{from TASKS.md}}
```
5. Fix small drift immediately. Flag larger updates for user decision.

## On-demand references

Loaded by mode instructions — do not read speculatively.

| File | Used by |
|------|---------|
| `references/claude-md-template.md` | Scaffold (CLAUDE.md creation) |
| `references/claude-md-nextjs.md` | Scaffold (Next.js projects) |
| `references/claude-md-go.md` | Scaffold (Go projects) |
| `references/claude-md-rust.md` | Scaffold (Rust projects) |
| `references/claude-md-fastapi.md` | Scaffold (FastAPI projects) |
| `references/claude-docs-scaffold.md` | Scaffold (.claude/ file creation) |
| `references/hooks-templates.md` | Scaffold (hook generation) |
| `references/rules-typescript.md` | Scaffold (TypeScript conventions) |
| `references/rules-go.md` | Scaffold (Go conventions) |
| `references/rules-rust.md` | Scaffold (Rust conventions) |
| `references/rules-python.md` | Scaffold (Python conventions) |
| `references/agent-code-reviewer.md` | Audit, Execute Phase B |
| `references/agent-security-reviewer.md` | Audit, Execute Phase B |
| `references/agent-build-resolver.md` | Execute Phase B (build failures) |
| `references/agent-performance-optimizer.md` | Audit, Execute Phase B |
| `references/agent-doc-updater.md` | Execute Phase D, Audit |
| `references/doc-evolution-rules.md` | Execute wrap-up, Audit |
| `.claude/LEARNINGS.md` | Execute (Phase A, if exists) |
