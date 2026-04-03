# CLAUDE.md

**dev-workflow-plugin** — A Claude Code plugin providing a reusable project workflow engine with git safety guardrails.
Built with Markdown skill definitions. Distributed as a Claude Code plugin.

## Hard Constraints

- **No code files** — this is a pure-markdown plugin. All logic lives in skill `.md` files and reference docs.
- **Skill file structure is load-bearing** — skill files must stay in `plugins/dev-workflow/skills/<skill-name>/` with exact naming (`skill.md` or `SKILL.md`).
- **Production-ready content only.** No placeholder text in shipped skill files.
- Do not modify `.claude-plugin/` manifests without explicit approval.

## North Star Metric

Reduce friction in Claude Code workflows — fewer wrong approaches, safer git operations, better session continuity.

## Documentation

All detailed docs in `.claude/`. Load only what you need per routing table below.

| File | Contents |
|------|----------|
| `.claude/ARCHITECTURE.md` | Plugin structure, directory layout, skill entry flows |
| `.claude/CONVENTIONS.md` | Skill file structure, naming, markdown formatting, versioning |
| `.claude/DECISIONS.md` | Why choices were made, rejected alternatives, tradeoffs |
| `.claude/TASKS.md` | Current phase, upcoming work, session handoff |
| `.claude/CHANGELOG.md` | Audit trail of doc updates |

### Context routing

| Task involves | Load |
|---------------|------|
| File structure, adding skills, plugin layout | ARCHITECTURE.md |
| Naming, file conventions, markdown formatting | CONVENTIONS.md |
| Understanding *why* something was built this way | DECISIONS.md |
| Phase progress, what's next, handoff | TASKS.md |

Never load all files speculatively.

### Doc evolution

Update `.claude/` files after tasks that change structure, conventions, or decisions. Log in CHANGELOG.md. Full rules in `plugins/dev-workflow/skills/dev-workflow/references/doc-evolution-rules.md`.

## Preferences

- Use agent teams for multi-task work.
- Respond concisely. No filler.
- Ask before assuming when decisions could go multiple ways.
- Test skill markdown for completeness before shipping.

## Commands

No build/test/lint commands — this is a markdown-only project.

Validation is manual: install the plugin locally and invoke skills.

```bash
claude plugins add /path/to/dev-workflow-plugin   # Install locally
claude /dev-workflow                                # Test main skill
claude /ship                                        # Test ship skill
claude /audit                                       # Test audit skill
claude /qa                                          # Test QA skill
```

## Git Workflow

- Default branch: `main`
- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `chore:`
- Push to the current branch only — never create new branches without explicit approval.
- Never stage `.env*` files, credentials, or secrets.
- Run `git status` + `git branch` before any git operation.
