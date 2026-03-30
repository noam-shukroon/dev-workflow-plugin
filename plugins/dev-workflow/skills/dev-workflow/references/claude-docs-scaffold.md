# .claude/ File Templates

Fill in `{{placeholders}}`. Adapt to project type — skip irrelevant sections.

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
├── {{source directories}}
└── {{config files}}
\```

## Database Schema
{{Tables, relationships — if applicable.}}

## Import Conventions
{{Import ordering, path aliases.}}
```

---

## CONVENTIONS.md

```markdown
# Conventions

## Styling
{{Framework, approach, design tokens.}}

## Code Patterns
- **Component structure**: Imports → Types → Component → hooks → handlers → render
- **Naming**: Files: {{kebab}}, Components: {{Pascal}}, Vars: {{camel}}, DB: {{snake}}
- **State**: {{Server/client/global approach.}}
- **Errors**: {{API error shape, validation.}}
- **TypeScript**: {{Strict mode, shared types location.}}

## Localization
{{Language, RTL/LTR, formats — if applicable.}}
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
```

---

## TOOLS.md

Task → tool mapping only. No descriptions (system loads those).

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
{{1-2 lines per session.}}
</details>
```
