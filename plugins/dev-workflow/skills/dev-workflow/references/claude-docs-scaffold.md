# .claude/ File Templates

Fill in `{{placeholders}}`. Adapt sections to your project type — skip irrelevant ones.

---

## ARCHITECTURE.md

```markdown
# Architecture

## Entry Flow
1. **{{Flow name}}**: `/path` → what happens → result

## Directory Layout

Legend: ✅ implemented, 📁 scaffolded, 🔮 planned

\```
project-root/
├── CLAUDE.md
├── .claude/
│   ├── ARCHITECTURE.md
│   ├── CONVENTIONS.md
│   ├── DECISIONS.md
│   ├── TASKS.md
│   ├── CHANGELOG.md
│   └── TOOLS.md
├── {{source directories}}
└── {{config files}}
\```

## Database Schema
{{Tables, columns, types, relationships — if applicable.}}

## Import Conventions
{{Import ordering, path aliases, barrel exports.}}
```

---

## CONVENTIONS.md

```markdown
# Conventions

## Styling
- **Framework**: {{e.g., Tailwind CSS}}
- **Approach**: {{e.g., Mobile-first}}
- **Colors/Typography/Spacing**: {{design tokens}}

## Code Patterns

### Component structure
\```
// Imports → Types → Component → Props → hooks → state → handlers → render
\```

### Naming
- Files: {{kebab-case}}, Components: {{PascalCase}}, Vars: {{camelCase}}, DB: {{snake_case}}

### State management
{{Server state, client state, global state approach.}}

### Error handling
{{API error shape, user messages, validation.}}

### TypeScript
{{Strict mode, type generation, shared types location.}}

## Localization
{{Language, RTL/LTR, currency, date/time format.}}
```

---

## DECISIONS.md

```markdown
# Decisions & Tradeoffs

## {{Decision title}}
{{What, why, alternatives rejected, tradeoffs.}}

## Archived
- **{{Decision}}** — {{one-line summary}}
```

---

## TASKS.md

```markdown
# Tasks

## Session Handoff

> Read CLAUDE.md first, then relevant .claude/ files per "When to read what" table.
> Identify relevant files before starting. Prefer inline source comments over re-scanning docs.

## Current Status
**Phase**: {{current}} | **Last**: {{completed}} | **Next**: {{upcoming}}

## Completed Phases
| Phase | Shipped | Sessions |
|-------|---------|----------|
| **1 · {{Name}}** | {{summary}} | {{N}} |

## Phase N · {{Name}}
- [ ] {{Task}}

## Common Recipes
- **Add a page** → {{instructions}}
- **Add a component** → {{instructions}}
- **Add an API route** → {{instructions}}
```

---

## TOOLS.md

Task → tool mapping only. System loads tool descriptions automatically.

```markdown
# Tools

| Task | Tool |
|------|------|
| {{task}} | {{tool}} |

## Project-Specific Notes
### {{tool-name}}
{{Project-specific tips/gotchas only.}}
```

---

## CHANGELOG.md

Recent entries visible. Archive older ones in `<details>`.

```markdown
# Documentation Changelog

## {{date}} — {{description}}
- **{{File}}**: What changed and why.

<details>
<summary>Archive: Sessions 1–N</summary>
{{1-2 lines per session or group by phase.}}
</details>
```
