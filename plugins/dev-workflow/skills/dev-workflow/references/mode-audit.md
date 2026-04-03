# Mode: Audit

Check the health and accuracy of `.claude/` documentation. For thorough review, also dispatch Code Reviewer and Doc Updater from `references/agents.md`.

Run checks 1-5 as **parallel subagents** (`superpowers: dispatching-parallel-agents`). Merge results into report at step 6.

1. **Inventory** — list `.claude/` files with line counts and last-modified dates.
2. **Cross-refs** — every file in CLAUDE.md tables exists; every `.claude/` file is referenced; routing table covers common tasks.
3. **Staleness** — ARCHITECTURE.md vs actual file tree; TASKS.md "in progress" items that are done; DECISIONS.md active items already shipped.
4. **Bloat** — count `##` headings per file. Flag 5+ unrelated top-level sections. Suggest specific splits.
5. **Quality** — irrelevant CONVENTIONS.md sections for project type; old CHANGELOG entries to archive; unmarked graduated LEARNINGS.
6. **Report**:
```
Doc Health: {{N}}/{{N}} files
Cross-refs: {{OK or issues}}
Stale: {{mismatches}}
Bloat: {{files to split}}
Actions: {{fixes needed}}
```
7. Apply small fixes directly. Present larger lists for user decision.
