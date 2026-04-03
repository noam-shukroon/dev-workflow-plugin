# dev-workflow

A Claude Code plugin that provides a reusable project workflow engine with git safety guardrails.

**Version:** 3.0.0

## What it does

Six modes via `/dev-workflow`:

| Mode | Purpose |
|------|---------|
| **Scaffold** | Set up a new project with living documentation, framework-specific templates, hooks, and language rules |
| **Execute** | Orchestrate agent teams for batch task execution with PRP-aware planning and agent delegation |
| **Handoff** | Generate session handoff prompts so the next session picks up seamlessly |
| **Resume** | Briefing on what changed since last session, with drift detection |
| **Audit** | Check `.claude/` documentation health using role-based subagents |
| **Upgrade** | Migrate existing `.claude/` docs to the latest plugin templates |

Three standalone skills:

| Skill | Purpose |
|-------|---------|
| `/ship` | 6-phase pre-flight verification → commit → push → optional PR creation |
| `/audit` | Structured project audit with parallel agents, numbered findings, and approval gate |
| `/qa` | Browser-based visual QA across desktop, tablet, and mobile viewports |

## v3.0 Features

### Scaffold-Generated Hooks
Profile-gated hook system (`minimal` / `standard` / `strict`) generated into user projects during scaffold. Includes:
- `block-no-verify` — prevents `--no-verify` on git commands
- `doc-file-warning` — warns on ad-hoc doc files (NOTES.md, TODO.md, etc.)
- `console-log-check` — catches leftover debug statements
- `cost-tracker` — logs session costs to `~/.claude/metrics/costs.jsonl`
- `session-persistence` — saves/restores working context between sessions
- `pre-commit-quality` — validates lint/tests before commit (strict only)
- `mcp-health-check` — tracks MCP failures with exponential cooldown warnings

### Framework-Specific Templates
Auto-detected during scaffold. Generates a tailored CLAUDE.md with framework best practices:

| Framework | Detection |
|-----------|-----------|
| Next.js | `next.config.*` |
| Go | `go.mod` |
| Rust | `Cargo.toml` |
| FastAPI | `pyproject.toml` with FastAPI dep |

### 6-Phase Ship Verification
Enhanced `/ship` pre-flight replaces the old build+test check:

1. **Build** — project build command (blocking)
2. **Type Check** — tsc, pyright, cargo check, go vet (blocking)
3. **Lint** — ESLint, ruff, clippy, golangci-lint (blocking)
4. **Test Suite** — project test command (blocking)
5. **Security Scan** — hardcoded secrets, .env refs, dangerous patterns (advisory)
6. **Diff Review** — large diff warnings, change summary (advisory)

Post-push PR creation via `gh pr create`.

### PRP Workflow
Product Requirements integrated into existing modes:
- **Scaffold** → optional PRD generation (`.claude/PRD.md`)
- **Execute** → Phase A reads PRD, generates dependency-aware implementation plan
- **Ship** → PR creation from conventional commits

### Git History Analysis
New Step 0.5 in scaffold auto-extracts patterns from git history:
- Commit conventions → CONVENTIONS.md
- File hotspots → TASKS.md recipes
- Workflow patterns → TASKS.md recipes

### Language-Specific Rules
Bundled rules merged into CONVENTIONS.md during scaffold:

| Language | Key rules |
|----------|-----------|
| TypeScript | strict mode, no `any`, Vitest, ESLint |
| Go | error wrapping, table-driven tests, small interfaces |
| Rust | no `unwrap()`, thiserror/anyhow, clippy pedantic |
| Python | type hints, ruff, pytest fixtures, Pydantic |

### Role-Based Subagents
5 specialized agents dispatched by `/audit` and execute mode:

| Agent | Focus |
|-------|-------|
| Code Reviewer | Bugs, quality, readability, conventions |
| Security Reviewer | OWASP top 10, secrets, auth/authz |
| Build Resolver | Diagnose and fix build failures |
| Performance Optimizer | N+1 queries, bundle size, memory |
| Doc Updater | Keep `.claude/` docs in sync |

### MCP Health Tracking
Hook-based failure tracking with exponential cooldown:
- Logs to `.claude/mcp-health.json`
- Warns after 3 consecutive failures
- Cooldown: 2min → 5min → 10min between warnings

## Core Features

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

/ship                  # 6-phase verify → commit → push → PR
/audit                 # Structured project audit
/qa                    # Browser-based visual QA
```

The `/dev-workflow` skill also triggers automatically when you mention "workflow", "scaffold docs", "agent team", "batch tasks", "handoff", or "resume".

## Skill Evolution

After every Execute run, the skill captures what worked and what didn't in `.claude/LEARNINGS.md`. Future runs read past learnings to avoid repeating mistakes. If the same pattern appears across 3+ projects, it gets flagged for inclusion in the skill itself.
