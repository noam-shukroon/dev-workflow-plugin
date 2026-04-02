# dev-workflow

A Claude Code plugin that provides a reusable project workflow engine with git safety guardrails.

**Version:** 2.2.0

## What it does

Six modes via `/dev-workflow`:

| Mode | Purpose |
|------|---------|
| **Scaffold** | Set up a new project with living documentation (`.claude/` files, `CLAUDE.md`) |
| **Execute** | Orchestrate agent teams for batch task execution with pre-commit verification |
| **Handoff** | Generate session handoff prompts so the next session picks up seamlessly |
| **Resume** | Briefing on what changed since last session, with drift detection |
| **Audit** | Check `.claude/` documentation health and accuracy |
| **Upgrade** | Migrate existing `.claude/` docs to the latest plugin templates |

Three standalone skills:

| Skill | Purpose |
|-------|---------|
| `/ship` | Build → test → commit → push with git safety guardrails |
| `/audit` | Structured project audit with parallel agents, numbered findings, and approval gate |
| `/qa` | Browser-based visual QA across desktop, tablet, and mobile viewports |

## Features

- Living documentation system with auto-evolution rules
- Agent team orchestration with dependency-aware batching
- Git safety guardrails — branch verification, `.env` protection, no force push, no silent branch creation
- Pre-commit verification — build and tests must pass before any commit
- Structured audit workflow with severity levels (P0/P1/P2) and approval gates
- Browser-based QA at three viewports using `claude-in-chrome`
- Skill evolution via per-project `LEARNINGS.md` — the workflow improves with use

## Installation

```bash
claude plugins add /path/to/dev-workflow-plugin
```

Or add via GitHub URL once published.

## Usage

Invoke directly with slash commands:

```
/dev-workflow          # Main workflow engine (auto-detects mode)
/dev-workflow scaffold # Scaffold .claude/ docs for a new project
/dev-workflow execute  # Run a batch of tasks with agent teams
/dev-workflow audit    # Audit .claude/ doc health

/ship                  # Build-verify-commit-push
/audit                 # Structured project audit
/qa                    # Browser-based visual QA
```

The `/dev-workflow` skill also triggers automatically when you mention "workflow", "scaffold docs", "agent team", "batch tasks", "handoff", or "resume".

## Skill Evolution

After every Execute run, the skill captures what worked and what didn't in `.claude/LEARNINGS.md`. Future runs read past learnings to avoid repeating mistakes. If the same pattern appears across 3+ projects, it gets flagged for inclusion in the skill itself.
