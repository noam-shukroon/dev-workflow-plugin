# Mode: Upgrade

Upgrade existing `.claude/` docs to match the latest plugin templates without overwriting project-specific content.

## Step 1: Inventory

Read existing CLAUDE.md and list all `.claude/` files with their `##` headings.

## Step 2: Diff against templates

Read `references/claude-md-template.md` and `references/claude-docs-scaffold.md`. Compare section-by-section:

| Check | Action |
|-------|--------|
| Missing section in CLAUDE.md (e.g., context routing table, preferences) | Add it, populated with project context |
| Missing `.claude/` file that template defines | Create from template, fill with project context |
| Existing section matches template | Skip — no change |
| Existing section has project-specific content not in template | Keep as-is |
| Template section has new fields vs existing | Merge new fields into existing section |

## Step 3: Apply upgrades

Use **parallel subagents** — one per file being upgraded.

For each file:
1. Read current content.
2. Identify missing sections/fields from template.
3. Insert new sections at the appropriate location. Preserve all existing content.
4. Do NOT rewrite or reformat existing sections.

## Step 4: Report

```
Upgrade complete:
- CLAUDE.md: {{added sections}}
- {{file}}: {{added sections}}
- No changes: {{files already up to date}}
```

Log all changes in `.claude/CHANGELOG.md`.

## Step 5: Verify

- All template sections present in CLAUDE.md (routing table, doc evolution note, preferences)
- All `.claude/` files referenced in CLAUDE.md tables
- No existing content was removed or rewritten
