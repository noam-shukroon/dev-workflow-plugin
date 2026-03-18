# Mode: Scaffold

Set up living documentation for a new or existing project.

## Step 0: Detect project context

Introspect before creating files:

1. **Project type** — check for `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `Gemfile`, `pom.xml`, etc.
2. **Commands** — extract build/test/lint from manifest. Pre-fill CLAUDE.md Commands section.
3. **Framework** — detect indicators (Next.js, FastAPI, Rails, etc.). Tailor CONVENTIONS.md — skip irrelevant sections.
4. **Monorepo** — check for `workspaces`, `lerna.json`, `nx.json`. Note structure in ARCHITECTURE.md.
5. **Existing docs** — check for README.md, CONTRIBUTING.md, existing .claude/. Incorporate, don't duplicate.

## Scaffold size

| Size | Files | Best for |
|------|-------|----------|
| **Minimal** | CLAUDE.md + ARCHITECTURE.md + TASKS.md | Small projects, CLIs, libraries |
| **Standard** | All 6 .claude/ files + CLAUDE.md | Most projects (default) |
| **Full** | Standard + PROMPT-TEMPLATE.md | Multi-person/agent team workflows |

Infer from project complexity, or ask the user.

## Step 1: Create CLAUDE.md

Read `references/claude-md-template.md` for the template. Include: project name, hard constraints, north star metric, documentation table, "when to read what" table, doc evolution rules (from `references/doc-evolution-rules.md`), preferences, commands, git workflow.

## Step 2: Create .claude/ files

Read `references/claude-docs-scaffold.md` for templates.

| File | Purpose |
|------|---------|
| `ARCHITECTURE.md` | File tree, directory layout, DB schema, entry flows, imports |
| `CONVENTIONS.md` | Styling, naming, code patterns, state, errors, types, i18n |
| `DECISIONS.md` | Choices made, rejected alternatives, tradeoffs. `## Archived` at bottom |
| `TASKS.md` | Phase checklist, status, handoff prompt, recipes |
| `CHANGELOG.md` | Dated audit trail of doc updates |
| `TOOLS.md` | Quick-reference table mapping tasks to tools. No tool descriptions (system loads those) |

## Step 3: Create PROMPT-TEMPLATE.md (full scaffold only)

Copy `references/prompt-template.md` to project root.

## Step 4: Verify

- All `.claude/` files for chosen size exist and are populated
- CLAUDE.md references exactly the created files (no dangling refs)
- Auto-detected commands actually work (run them)
- "When to read what" table covers common task types
