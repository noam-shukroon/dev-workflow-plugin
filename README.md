# dev-workflow

A Claude Code plugin that provides a reusable project workflow engine.

## What it does

Three modes:

1. **Scaffold** — Set up a new project with a living documentation system (`.claude/` files, `CLAUDE.md`, prompt templates)
2. **Execute** — Orchestrate agent teams to run batches of tasks in parallel/sequential order with built-in verification
3. **Handoff** — Generate session handoff prompts so the next session picks up where you left off

## Features

- Living documentation system with auto-evolution rules
- Agent team orchestration with dependency-aware batching
- Skill evolution via per-project `LEARNINGS.md` — the workflow improves with use
- Reference templates for `CLAUDE.md`, `.claude/` files, and task prompts

## Installation

```bash
claude plugins add /path/to/dev-workflow-plugin
```

Or add via GitHub URL once published.

## Usage

The skill triggers automatically when you:
- Start a new project and need documentation scaffolding
- Have a batch of tasks to execute with agent teams
- Need to hand off between sessions
- Mention "workflow", "scaffold docs", "agent team", "batch tasks", etc.

You can also invoke it directly:
```
/dev-workflow
```

## Skill Evolution

After every Execute run, the skill captures what worked and what didn't in `.claude/LEARNINGS.md`. Future runs read past learnings to avoid repeating mistakes. If the same pattern appears across 3+ projects, it gets flagged for inclusion in the skill itself.
