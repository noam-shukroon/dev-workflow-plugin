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

## Task Sizing

Scale the workflow to match task complexity:

- **1 task, ≤3 files**: Execute directly with build/test gate. Skip Phase A planning.
- **2-4 tasks**: Standard batching, skip session splitting (no Phase A step 6).
- **5+ tasks**: Full Phase A with dependency graph and session splitting.

## Modes

Detect from context. If unclear, ask.

| Mode | Use when | Read |
|------|----------|------|
| **Scaffold** | Starting a project or adding .claude/ docs | `references/mode-scaffold.md` |
| **Execute** | Given a batch of tasks to implement | `references/mode-execute.md` |
| **Handoff** | Preparing for a session break | `references/mode-handoff.md` |
| **Resume** | Returning to a project after a break | `references/mode-resume.md` |
| **Audit** | Checking documentation health | `references/mode-audit.md` |
| **Upgrade** | Upgrading .claude/ docs to latest plugin templates | `references/mode-upgrade.md` |

After detecting the mode, read **only** that mode's reference file, then follow its instructions.

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
| `references/agents.md` | Execute, Audit (role-based agent dispatch) |
| `references/doc-evolution-rules.md` | Execute wrap-up, Audit |
| `.claude/LEARNINGS.md` | Execute (Phase A, if exists) |
