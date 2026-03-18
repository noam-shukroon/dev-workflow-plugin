# Mode: Audit

Check the health and accuracy of the `.claude/` documentation system.

1. **Inventory** — list `.claude/` files with line counts and last-modified dates.
2. **Cross-refs** — every file in CLAUDE.md tables exists; every `.claude/` file is referenced; "when to read what" covers common tasks.
3. **Staleness** — ARCHITECTURE.md vs actual file tree; TASKS.md "in progress" items that are done; DECISIONS.md active items already shipped.
4. **Bloat check** — for each file, count `##` headings and check if they cover unrelated concerns. Flag files with 5+ unrelated top-level sections. Suggest specific splits (e.g., "CONVENTIONS.md has styling, code patterns, API contracts, and error handling — split API.md out").
5. **Quality** — irrelevant CONVENTIONS.md sections for project type; old CHANGELOG entries to archive; unmarked graduated LEARNINGS.
6. **Report**:
```
Doc Health: 6/6 files
Cross-refs: OK
Stale: ARCHITECTURE.md missing 3 files
Bloat: CONVENTIONS.md has 7 unrelated sections — split DESIGN.md out
Actions: [list fixes needed]
```
7. Apply small fixes directly. Present larger lists for user decision.
