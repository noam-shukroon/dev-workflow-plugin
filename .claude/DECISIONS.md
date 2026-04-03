# Decisions & Tradeoffs

## Pure-markdown plugin architecture
All logic lives in `.md` skill files and reference docs — no code files (JS, Python, etc.). This means the plugin has zero dependencies, zero build steps, and instant installation. The tradeoff is that complex logic must be expressed as instructions for Claude rather than executable code. Accepted because Claude Code skills are fundamentally prompt-based, and adding code would create maintenance burden without meaningful capability gains.

## Mode-based routing in SKILL.md
The main dev-workflow skill detects the user's intent (scaffold, execute, handoff, resume, audit, upgrade) and loads only the relevant reference file. This keeps the initial skill load small and avoids wasting context on irrelevant instructions. Alternative considered: separate skills per mode. Rejected because the modes share common defaults (git safety, context loading) and a single entry point is simpler for users.

## Lazy-loaded reference files
Reference docs (`references/*.md`) are loaded on-demand by skills, never speculatively. This was a deliberate refactoring (commit `1e28f39`) after the initial monolithic skill grew too large. Tradeoff: skills must explicitly name which references to load, adding some maintenance overhead when references are added/renamed.

## Framework-specific CLAUDE.md templates
Scaffold mode detects project framework (Next.js, Go, Rust, FastAPI) and uses a tailored template instead of the generic one. This front-loads best practices for each ecosystem. Tradeoff: each template must be maintained separately, and framework detection heuristics can be wrong for unusual project structures.

## Profile-gated hooks
Hook generation in scaffold mode is gated by profile level (minimal/standard/strict). This prevents overwhelming simple projects with hooks they don't need while giving advanced users full safety guardrails. Alternative: always generate all hooks. Rejected because over-hooking causes friction and users disable everything.

## Archived
- **Monolithic skill file** — initial approach, replaced by mode-based lazy loading in v2.0.0 refactor
