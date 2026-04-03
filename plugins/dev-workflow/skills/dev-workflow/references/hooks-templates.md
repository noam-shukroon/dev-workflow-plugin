# Hooks Templates

Generated into `.claude/hooks/` during scaffold mode. Registered in `.claude/settings.json`. Profile controlled by `ECC_HOOK_PROFILE` env var (default: `standard`).

## Profiles

| Profile | Hooks |
|---------|-------|
| `minimal` | block-no-verify, cost-tracker |
| `standard` | All minimal + doc-file-warning, console-log-check, session-persistence, mcp-health-check |
| `strict` | All standard + pre-commit-quality |

---

## block-no-verify
**Event:** PreToolUse | **Tool:** Bash | **Profile:** all
Block git commands containing `--no-verify` or `--no-gpg-sign`. Exit 2 with explanation.

## cost-tracker
**Event:** Stop | **Profile:** all
Append session cost JSON (`timestamp`, `model`, `input_tokens`, `output_tokens`, `cost`) to `~/.claude/metrics/costs.jsonl`. Create dir if missing.

## doc-file-warning
**Event:** PreToolUse | **Tool:** Write | **Profile:** standard+
Block creation of scratch files (NOTES.md, TODO.md, SCRATCH.md, TEMP.md). Exit 2 suggesting .claude/TASKS.md or inline comments instead.

## console-log-check
**Event:** Stop | **Profile:** standard+
Scan `git diff --name-only HEAD` files for `console.log`, `debugger`, `print(`. Warn on findings, don't block.

## session-persistence
**Event:** Stop (save) + SessionStart (restore) | **Profile:** standard+

**Save (Stop):** Write `.claude/session-state.json` with branch, modified files, staged files, last task, timestamp.

**Restore (SessionStart):** If `.claude/session-state.json` exists, print previous session state (branch, last task, saved time, modified/staged files).

## mcp-health-check
**Event:** PostToolUseFailure | **Profile:** standard+
Log MCP tool failures to `.claude/mcp-health.json` (keep last 50). After 3 consecutive failures, warn and suggest `claude mcp restart`. Exponential cooldown on warnings (2min → 5min → 10min).

## pre-commit-quality
**Event:** PreToolUse | **Tool:** Bash | **Profile:** strict only
Intercept `git commit` commands. Auto-detect and run lint + test commands from project manifest. Block commit on failure.

---

## Generation Instructions

1. Detect profile from `ECC_HOOK_PROFILE` (default: `standard`).
2. Create `.claude/hooks/` directory.
3. Generate each hook as a bash script: `set -euo pipefail`, profile comment header, logic per spec above. Replace `{{project_name}}` and `{{profile}}`.
4. `chmod +x` each hook file.
5. Merge registrations into `.claude/settings.json` (append, don't overwrite user hooks).
6. Skip hooks with user modifications (check `.claude/hooks/.generated` manifest).
