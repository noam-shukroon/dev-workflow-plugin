# Conventions

## Skill File Structure
- Skills live in `plugins/dev-workflow/skills/<skill-name>/`
- Entry file: `skill.md` or `SKILL.md` (case-insensitive, but be consistent per skill)
- Reference docs: `references/<descriptive-name>.md` within the skill directory
- One skill per directory — never combine multiple slash commands in one skill

## Naming
- **Skill directories**: kebab-case (`dev-workflow`, `qa`)
- **Reference files**: kebab-case with descriptive prefixes (`mode-scaffold.md`, `claude-md-nextjs.md`, `agent-code-reviewer.md`, `rules-typescript.md`)
- **Prefix conventions**: `mode-` for mode instructions, `claude-md-` for CLAUDE.md templates, `agent-` for subagent definitions, `rules-` for language rules, `hooks-` for hook templates, `claude-docs-` for doc scaffolding

## Markdown Formatting
- Use ATX headings (`#`, `##`, `##`) — never setext (underlines)
- Tables for structured data; bullet lists for sequential/unordered items
- Code blocks with language hints where applicable (` ```bash `, ` ```markdown `)
- Placeholders in templates use `{{double-braces}}`
- No trailing whitespace, no multiple consecutive blank lines

## Versioning
- Plugin version in both `plugins/dev-workflow/.claude-plugin/plugin.json` AND `.claude-plugin/marketplace.json` — must stay in sync
- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `chore:`
- Version bumps are `chore:` commits

## Content Standards
- No placeholder text in shipped skill files — all content must be production-ready
- Reference files should be self-contained: a skill loading one reference shouldn't need to load another to understand it
- Skills should declare which references they load in an on-demand table
