# Tasks

## Current Status
**Phase**: v3.0.0 shipped | **Last**: 8-feature expansion (hooks, framework templates, verification loop, PRP, git analysis, language rules, subagents, MCP health) | **Next**: User-driven improvements

## Completed Phases

| Phase | Shipped | Sessions |
|-------|---------|----------|
| **1 - Initial Plugin** | Core dev-workflow skill with scaffold/execute/handoff/resume modes | — |
| **2 - v2.0.0** | Plugin restructure, version bump | — |
| **3 - v2.2.0** | Ship, audit, QA skills; git safety guardrails; pre-commit checks; upgrade mode; README update | — |
| **4 - v3.0.0** | Hooks system, framework templates (Next.js/Go/Rust/FastAPI), 6-phase ship verification, PRP workflow, git history analysis, language rules (TS/Go/Rust/Python), 5 role-based subagents, MCP health tracking; version alignment (marketplace + plugin) | — |

## Backlog
- [ ] Publish plugin to Claude Code marketplace
- [ ] Add automated skill validation (lint skill files for completeness)
- [ ] Update README.md for v3.0.0 features

## Common Recipes
- **Add a new skill** → Create `plugins/dev-workflow/skills/<name>/skill.md`, update plugin.json if needed, update README.md
- **Add a reference doc** → Create in `plugins/dev-workflow/skills/dev-workflow/references/`, reference from SKILL.md on-demand table
- **Bump version** → Update both `plugins/dev-workflow/.claude-plugin/plugin.json` AND `.claude-plugin/marketplace.json`
- **Add a new hook** → Add template to `references/hooks-templates.md`, assign profile level, add settings.json snippet
- **Add a new agent** → Create `references/agent-<role>.md`, add to SKILL.md on-demand table, wire into audit/execute modes
- **Add framework template** → Create `references/claude-md-<framework>.md`, add detection rule to `mode-scaffold.md` Step 0.3
- **Add language rules** → Create `references/rules-<language>.md`, add detection rule to `mode-scaffold.md` Step 0.4
