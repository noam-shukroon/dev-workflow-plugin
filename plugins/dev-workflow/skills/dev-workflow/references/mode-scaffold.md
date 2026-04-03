# Mode: Scaffold

Set up living documentation for a new or existing project.

## Step 0: Detect project context

Introspect before creating files:

1. **Project type** — check for `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `Gemfile`, `pom.xml`, etc.
2. **Commands** — extract build/test/lint from manifest. Pre-fill CLAUDE.md Commands section.
3. **Framework** — detect indicators and select matching CLAUDE.md template:

| Indicator | Framework | Template |
|-----------|-----------|----------|
| `next.config.*` | Next.js | `references/claude-md-nextjs.md` |
| `go.mod` | Go | `references/claude-md-go.md` |
| `Cargo.toml` | Rust | `references/claude-md-rust.md` |
| `pyproject.toml` with FastAPI dep | FastAPI | `references/claude-md-fastapi.md` |
| Other / none | Generic | `references/claude-md-template.md` |

4. **Language detection** — identify primary languages for rules merge (Step 2b):

| Indicator | Language | Rules file |
|-----------|----------|------------|
| `tsconfig.json` or `.ts` files | TypeScript | `references/rules-typescript.md` |
| `go.mod` | Go | `references/rules-go.md` |
| `Cargo.toml` | Rust | `references/rules-rust.md` |
| `pyproject.toml` or `.py` files | Python | `references/rules-python.md` |

5. **Monorepo** — check for `workspaces`, `lerna.json`, `nx.json`. Note structure in ARCHITECTURE.md.
6. **Existing docs** — check for README.md, CONTRIBUTING.md, existing .claude/. Incorporate, don't duplicate.

## Step 0.5: Git history analysis

If the project has git history (>10 commits), auto-extract patterns:

1. `git log --oneline -50` → detect commit conventions (conventional commits? prefix style? casing?). Pre-populate CONVENTIONS.md commit section.
2. `git log --stat -20` → identify file hotspots (most-changed files/dirs). Add as "Common Recipes" in TASKS.md.
3. Detect common workflows from commit sequences (e.g., "feat → test → fix → ship" patterns). Add relevant recipes to TASKS.md.

Skip this step for new projects with <10 commits.

## Scaffold size

| Size | Files | Best for |
|------|-------|----------|
| **Minimal** | CLAUDE.md + ARCHITECTURE.md + TASKS.md | Small projects, CLIs, libraries |
| **Standard** | All 6 .claude/ files + CLAUDE.md | Most projects (default) |
| **Full** | Standard + PROMPT-TEMPLATE.md + hooks + agents | Multi-person/agent team workflows |

Infer from project complexity, or ask the user.

## Step 1: Create CLAUDE.md

Use the framework-specific template if detected (Step 0.3), otherwise read `references/claude-md-template.md`. Include: project name, hard constraints, north star metric, documentation table, context routing table, doc evolution note, preferences, commands, git workflow.

## Step 2: Create .claude/ files

Read `references/claude-docs-scaffold.md` for templates. Use **parallel subagents** — one per file:

| File | Purpose |
|------|---------|
| `ARCHITECTURE.md` | File tree, directory layout, DB schema, entry flows, imports |
| `CONVENTIONS.md` | Styling, naming, code patterns, state, errors, types, i18n |
| `DECISIONS.md` | Choices made, rejected alternatives, tradeoffs. `## Archived` at bottom |
| `TASKS.md` | Phase checklist, status, handoff prompt, recipes |
| `CHANGELOG.md` | Dated audit trail of doc updates |
| `TOOLS.md` | Quick-reference table mapping tasks to tools. No tool descriptions |

### Step 2b: Merge language rules

For each detected language (Step 0.4), read its rules file and merge into CONVENTIONS.md. Multiple languages = multiple sections. The rules provide opinionated defaults — adjust to match existing project patterns found in Step 0.5.

## Step 3: Create PROMPT-TEMPLATE.md (full scaffold only)

Create a task-listing template at project root:

```markdown
# Task Prompt

## Setup
Read `CLAUDE.md` first, then load relevant `.claude/` files per routing table.

## Tasks
{{List each task:
- **ID**: T1, T2, ...
- **Title**: what to do
- **Files**: affected files
- **Details**: exact values, class names, specifics
- **Size**: S / M / L}}
```

## Step 4: Generate hooks (standard+ scaffold)

Read `references/hooks-templates.md` for all hook templates.

1. Ask user for hook profile: **minimal** (safety only), **standard** (default), **strict** (all hooks).
2. Create `hooks/` directory in the project.
3. Generate each hook matching the chosen profile, resolving `{{placeholder}}` values.
4. Register hooks in `.claude/settings.json`.
5. Add comment block to each generated hook explaining the profile system and how to customize.

Skip for minimal scaffold size.

## Step 5: Generate agent definitions (full scaffold only)

Copy role-based agent definitions into the project for use with `/audit` and execute mode. All agents are defined in `references/agents.md`.

## Step 6: Optional PRD generation

Offer to create `.claude/PRD.md` for non-trivial projects:

```markdown
# Product Requirements Document

## Problem
{{What problem does this solve?}}

## Users
{{Who are the target users?}}

## Requirements
{{Numbered list of must-have requirements}}

## Success Metrics
{{How will you measure success?}}

## Out of Scope
{{What this project explicitly does NOT include}}
```

If the user provides a project description, pre-fill sections. Execute mode Phase A will read this PRD if present.

## Step 7: Verify

- All `.claude/` files for chosen size exist and are populated
- CLAUDE.md references exactly the created files (no dangling refs)
- Auto-detected commands actually work (run them)
- Context routing table covers common task types
- Language rules merged correctly (if applicable)
- Hooks registered and functional (if generated)
