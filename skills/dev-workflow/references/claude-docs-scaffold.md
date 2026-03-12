# .claude/ File Templates

Templates for each file in the `.claude/` documentation system. Fill in `{{placeholders}}` for your project.

---

## ARCHITECTURE.md

```markdown
# Architecture

## Entry Flow

{{Describe the main user flows through the app. Example:}}
1. **Customer flow**: `/path` → what happens → result
2. **Admin flow**: `/path` → auth → dashboard

## Directory Layout

Legend: ✅ = implemented, 📁 = scaffolded (empty), 🔮 = planned (not yet created)

\```
project-root/
├── CLAUDE.md                       # ✅ Hard constraints + documentation map
├── .claude/                        # ✅ Living documentation
│   ├── ARCHITECTURE.md             # ✅ This file
│   ├── CONVENTIONS.md              # ✅ Coding standards
│   ├── DECISIONS.md                # ✅ Design decisions
│   ├── TASKS.md                    # ✅ Phase checklist
│   ├── CHANGELOG.md                # ✅ Documentation audit trail
│   └── TOOLS.md                    # ✅ Plugins, skills, MCP servers
├── {{your source directories}}
└── {{config files}}
\```

## Database Schema

{{If applicable — document tables, columns, types, relationships.}}

## Import Conventions

{{Import ordering rules, path aliases, barrel export patterns.}}
```

---

## CONVENTIONS.md

```markdown
# Conventions

## Styling

- **Framework**: {{e.g., Tailwind CSS}}
- **Approach**: {{e.g., Mobile-first}}
- **Color system**: {{Describe your design tokens}}
- **Typography**: {{Font stack, sizing approach}}
- **Spacing**: {{Spacing conventions}}
- **Components**: {{Component library if any}}

## Code Patterns

### Component structure
\```
// 1. Imports
// 2. Types/interfaces
// 3. Component definition
// 4. Props → hooks → derived state → handlers → render
\```

### Naming conventions
- **Files**: {{e.g., kebab-case}}
- **Components**: {{e.g., PascalCase}}
- **Variables/functions**: {{e.g., camelCase}}
- **Types/interfaces**: {{e.g., PascalCase with context prefix}}
- **Database columns**: {{e.g., snake_case}}

### State management
{{How you manage state — server state, client state, global state.}}

### Error handling
{{Error handling patterns — API shape, user messages, validation.}}

### TypeScript
{{Strict mode, type generation, shared types location.}}

## Localization

{{Language, RTL/LTR, currency, phone format, date format, time format.}}
```

---

## DECISIONS.md

```markdown
# Decisions & Tradeoffs

## {{Decision title}}
{{What was decided. Why. What alternatives were considered and rejected. Known tradeoffs.}}

## Archived

<!-- Moved here when no longer relevant.
     Format: ## [Title] (archived YYYY-MM-DD — [reason]) -->
```

---

## TASKS.md

```markdown
# Tasks

## Session Handoff Prompt

Copy this into the first message of every new Claude session:

> Read CLAUDE.md first — it contains hard constraints and a documentation map.
> Then read only the `.claude/` files relevant to your task (see the "When to read what" table).
>
> Before starting any task:
> - Identify which .claude/ files are relevant and read only those.
> - For routine tasks, you should need zero exploratory file reads beyond what you are directly editing.
> - Prefer inline comments in source files over re-scanning .claude/ files for implementation details.

## Current Status

**Phase**: {{current phase}}
**Last completed**: {{what was just finished}}
**Next up**: {{what needs to happen next}}
**Stack**: {{tech stack with versions}}

## Phase Checklist

### Phase 1 · {{Name}} ({{Timeline}})
- [ ] {{Task}}
- [ ] {{Task}}

### Phase 2 · {{Name}} ({{Timeline}})
- [ ] {{Task}}

## Common Task Recipes

- **Add a new page** → {{instructions}}
- **Add a new component** → {{instructions}}
- **Add an API route** → {{instructions}}
```

---

## TOOLS.md

```markdown
# Tools — Plugins, Skills & MCP Servers

## Plugins

### {{plugin-name}}
**What it does**: {{description}}
**Skill/Command**: {{how to trigger}}
**When to use**: {{specific scenarios}}

## MCP Servers

### {{server-name}}
**Status**: Connected
**What it does**: {{description}}
**Key tools**: {{list main tools}}

## When to Use What

| Task | Tool |
|------|------|
| {{task type}} | {{tool name}} |
```

---

## CHANGELOG.md

```markdown
# Documentation Changelog

## {{date}} (session 1 — project setup)
- **CLAUDE.md**: Created with project details, hard constraints, documentation map.
- **ARCHITECTURE.md**: Added entry flows, directory layout, DB schema.
- **CONVENTIONS.md**: Added styling, code patterns, naming, localization.
- **DECISIONS.md**: Documented initial design decisions.
- **TASKS.md**: Added phase checklist and current status.
- **TOOLS.md**: Documented all installed plugins, skills, MCP servers.
- **CHANGELOG.md**: This entry.
```
