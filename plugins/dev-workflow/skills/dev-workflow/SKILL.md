---
name: dev-workflow
description: "Project workflow engine: scaffolds .claude/ docs, orchestrates agent teams, standardizes verification, supports session resume and doc audits. Trigger on: scaffold, execute, handoff, resume, audit, agent team, workflow, batch, project setup, or multi-task coordination. Also trigger when the user asks to plan, batch, or coordinate multiple tasks — even if they don't name this skill explicitly."
---

# Dev Workflow

Detect which mode from context. If unclear, ask.

| Mode | Use when | Read |
|------|----------|------|
| **Scaffold** | Starting a project or adding .claude/ docs | `references/mode-scaffold.md` |
| **Execute** | Given a batch of tasks to implement | `references/mode-execute.md` |
| **Handoff** | Preparing for a session break | `references/mode-handoff.md` |
| **Resume** | Returning to a project after a break | `references/mode-resume.md` |
| **Audit** | Checking documentation health | `references/mode-audit.md` |

After detecting the mode, read **only** that mode's reference file, then follow its instructions.

## Shared references

These are loaded on-demand by mode instructions — do not read speculatively.

| File | Used by |
|------|---------|
| `references/claude-md-template.md` | Scaffold (CLAUDE.md creation) |
| `references/claude-docs-scaffold.md` | Scaffold (.claude/ file creation) |
| `references/prompt-template.md` | Scaffold (full size only) |
| `references/doc-evolution-rules.md` | Execute wrap-up, Audit |
| `.claude/LEARNINGS.md` | Execute (Phase A, if exists) |

## Doc evolution (summary)

After any task that changes structure, conventions, or decisions — update the relevant `.claude/` file in the same session. Log every update in CHANGELOG.md. After updating, check if the file now has 5+ unrelated `##` sections — if so, split it. Full rules in `references/doc-evolution-rules.md`.
