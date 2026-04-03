# Hooks Templates

These templates are generated into user projects during scaffold mode. Each hook file is placed in the project's `.claude/hooks/` directory and registered in `.claude/settings.json`. Hooks are gated by the `ECC_HOOK_PROFILE` environment variable, which controls which hooks are active for a given project. If `ECC_HOOK_PROFILE` is unset, the `standard` profile is used by default.

## Profile System

| Profile | Level | Hooks Included |
|---------|-------|----------------|
| `minimal` | Safety only | `block-no-verify`, `cost-tracker` |
| `standard` | Default | All minimal hooks + `doc-file-warning`, `console-log-check`, `session-persistence`, `mcp-health-check` |
| `strict` | Full enforcement | All standard hooks + `pre-commit-quality` |

Set the profile in your shell environment:

```bash
export ECC_HOOK_PROFILE=standard  # minimal | standard | strict
```

---

## Hook: block-no-verify

| Field | Value |
|-------|-------|
| Event | `PreToolUse` |
| Tool | `Bash` |
| Profile | all (`minimal`, `standard`, `strict`) |
| Purpose | Prevent bypassing git hooks and GPG signing |

### Template

```bash
#!/usr/bin/env bash
# Hook: block-no-verify
# Generated for: {{project_name}}
# Profile: {{profile}}
# Blocks --no-verify and --no-gpg-sign flags on git commands

set -euo pipefail

COMMAND="$1"

# Only inspect git commands
if [[ "$COMMAND" != git\ * ]]; then
  exit 0
fi

if echo "$COMMAND" | grep -qE -- '--no-verify|--no-gpg-sign'; then
  echo "BLOCK: git safety flags --no-verify and --no-gpg-sign are not allowed."
  echo "These flags bypass critical safety checks. Remove the flag and retry."
  exit 2
fi

exit 0
```

### settings.json registration

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [".claude/hooks/block-no-verify.sh"]
      }
    ]
  }
}
```

---

## Hook: doc-file-warning

| Field | Value |
|-------|-------|
| Event | `PreToolUse` |
| Tool | `Write` |
| Profile | `standard`, `strict` |
| Purpose | Warn when creating scratch/temp documentation files that add noise |

### Template

```bash
#!/usr/bin/env bash
# Hook: doc-file-warning
# Generated for: {{project_name}}
# Profile: {{profile}}
# Warns on creation of common scratch doc files

set -euo pipefail

FILE_PATH="$1"
BASENAME=$(basename "$FILE_PATH" | tr '[:lower:]' '[:upper:]')

BLOCKED_FILES=("NOTES.MD" "TODO.MD" "SCRATCH.MD" "TEMP.MD")

for BLOCKED in "${BLOCKED_FILES[@]}"; do
  if [[ "$BASENAME" == "$BLOCKED" ]]; then
    echo "WARNING: Creating $FILE_PATH is discouraged."
    echo "These scratch files add noise to the repository. Consider using .claude/TASKS.md or inline comments instead."
    exit 2
  fi
done

exit 0
```

### settings.json registration

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write",
        "hooks": [".claude/hooks/doc-file-warning.sh"]
      }
    ]
  }
}
```

---

## Hook: console-log-check

| Field | Value |
|-------|-------|
| Event | `Stop` |
| Tool | — |
| Profile | `standard`, `strict` |
| Purpose | Scan staged changes for debug statements before session ends |

### Template

```bash
#!/usr/bin/env bash
# Hook: console-log-check
# Generated for: {{project_name}}
# Profile: {{profile}}
# Scans git diff for leftover debug statements

set -euo pipefail

DIFF_OUTPUT=$(git diff --name-only HEAD 2>/dev/null || true)

if [[ -z "$DIFF_OUTPUT" ]]; then
  exit 0
fi

FINDINGS=""

while IFS= read -r FILE; do
  [[ -f "$FILE" ]] || continue

  MATCHES=$(grep -nE '(console\.log|debugger|print\()' "$FILE" 2>/dev/null || true)
  if [[ -n "$MATCHES" ]]; then
    FINDINGS+="  $FILE:"$'\n'"$MATCHES"$'\n\n'
  fi
done <<< "$DIFF_OUTPUT"

if [[ -n "$FINDINGS" ]]; then
  echo "WARNING: Debug statements found in modified files:"
  echo ""
  echo "$FINDINGS"
  echo "Review these before committing. They may be intentional, but verify."
fi

exit 0
```

### settings.json registration

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [".claude/hooks/console-log-check.sh"]
      }
    ]
  }
}
```

---

## Hook: cost-tracker

| Field | Value |
|-------|-------|
| Event | `Stop` |
| Tool | — |
| Profile | all (`minimal`, `standard`, `strict`) |
| Purpose | Track session cost metrics for budgeting and analysis |

### Template

```bash
#!/usr/bin/env bash
# Hook: cost-tracker
# Generated for: {{project_name}}
# Profile: {{profile}}
# Appends session cost data to ~/.claude/metrics/costs.jsonl

set -euo pipefail

METRICS_DIR="$HOME/.claude/metrics"
METRICS_FILE="$METRICS_DIR/costs.jsonl"

mkdir -p "$METRICS_DIR"

TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
MODEL="${CLAUDE_MODEL:-unknown}"
INPUT_TOKENS="${CLAUDE_INPUT_TOKENS:-0}"
OUTPUT_TOKENS="${CLAUDE_OUTPUT_TOKENS:-0}"
TOTAL_COST="${CLAUDE_TOTAL_COST:-0.00}"

echo "{\"timestamp\":\"$TIMESTAMP\",\"model\":\"$MODEL\",\"input_tokens\":$INPUT_TOKENS,\"output_tokens\":$OUTPUT_TOKENS,\"cost\":$TOTAL_COST}" >> "$METRICS_FILE"

exit 0
```

### settings.json registration

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [".claude/hooks/cost-tracker.sh"]
      }
    ]
  }
}
```

---

## Hook: session-persistence

| Field | Value |
|-------|-------|
| Event | `Stop` + `SessionStart` |
| Tool | — |
| Profile | `standard`, `strict` |
| Purpose | Save and restore session context across Claude Code sessions |

### Stop Template (save state)

```bash
#!/usr/bin/env bash
# Hook: session-persistence (save)
# Generated for: {{project_name}}
# Profile: {{profile}}
# Saves session state on exit for continuity

set -euo pipefail

STATE_FILE=".claude/session-state.json"
mkdir -p .claude

BRANCH=$(git branch --show-current 2>/dev/null || echo "unknown")
MODIFIED_FILES=$(git diff --name-only 2>/dev/null | head -20 | jq -R . | jq -s . 2>/dev/null || echo "[]")
STAGED_FILES=$(git diff --cached --name-only 2>/dev/null | head -20 | jq -R . | jq -s . 2>/dev/null || echo "[]")
LAST_TASK="${CLAUDE_LAST_TASK:-unknown}"
TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

cat > "$STATE_FILE" <<EOJSON
{
  "saved_at": "$TIMESTAMP",
  "branch": "$BRANCH",
  "modified_files": $MODIFIED_FILES,
  "staged_files": $STAGED_FILES,
  "last_task": "$LAST_TASK"
}
EOJSON

exit 0
```

### SessionStart Template (restore state)

```bash
#!/usr/bin/env bash
# Hook: session-persistence (restore)
# Generated for: {{project_name}}
# Profile: {{profile}}
# Displays saved session state at startup

set -euo pipefail

STATE_FILE=".claude/session-state.json"

if [[ ! -f "$STATE_FILE" ]]; then
  exit 0
fi

echo "Previous session state:"
echo "  Branch: $(jq -r '.branch' "$STATE_FILE" 2>/dev/null || echo 'unknown')"
echo "  Last task: $(jq -r '.last_task' "$STATE_FILE" 2>/dev/null || echo 'unknown')"
echo "  Saved at: $(jq -r '.saved_at' "$STATE_FILE" 2>/dev/null || echo 'unknown')"

MODIFIED=$(jq -r '.modified_files[]?' "$STATE_FILE" 2>/dev/null || true)
if [[ -n "$MODIFIED" ]]; then
  echo "  Modified files:"
  echo "$MODIFIED" | sed 's/^/    /'
fi

STAGED=$(jq -r '.staged_files[]?' "$STATE_FILE" 2>/dev/null || true)
if [[ -n "$STAGED" ]]; then
  echo "  Staged files:"
  echo "$STAGED" | sed 's/^/    /'
fi

exit 0
```

### settings.json registration

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [".claude/hooks/session-persistence-save.sh"]
      }
    ],
    "SessionStart": [
      {
        "hooks": [".claude/hooks/session-persistence-restore.sh"]
      }
    ]
  }
}
```

---

## Hook: pre-commit-quality

| Field | Value |
|-------|-------|
| Event | `PreToolUse` |
| Tool | `Bash` |
| Profile | `strict` only |
| Purpose | Run lint and test checks before allowing git commit |

### Template

```bash
#!/usr/bin/env bash
# Hook: pre-commit-quality
# Generated for: {{project_name}}
# Profile: {{profile}}
# Runs lint/test before git commit is allowed

set -euo pipefail

COMMAND="$1"

# Only intercept git commit commands
if ! echo "$COMMAND" | grep -qE '^git commit'; then
  exit 0
fi

PROJECT_DIR=$(git rev-parse --show-toplevel 2>/dev/null || pwd)

# Detect and run lint command
LINT_CMD=""
if [[ -f "$PROJECT_DIR/package.json" ]] && grep -q '"lint"' "$PROJECT_DIR/package.json" 2>/dev/null; then
  LINT_CMD="npm run lint"
elif [[ -f "$PROJECT_DIR/Makefile" ]] && grep -q '^lint:' "$PROJECT_DIR/Makefile" 2>/dev/null; then
  LINT_CMD="make lint"
elif [[ -f "$PROJECT_DIR/pyproject.toml" ]]; then
  LINT_CMD="ruff check ."
fi

if [[ -n "$LINT_CMD" ]]; then
  echo "Running lint: $LINT_CMD"
  if ! eval "$LINT_CMD"; then
    echo "BLOCK: Lint failed. Fix lint errors before committing."
    exit 2
  fi
fi

# Detect and run test command
TEST_CMD=""
if [[ -f "$PROJECT_DIR/package.json" ]] && grep -q '"test"' "$PROJECT_DIR/package.json" 2>/dev/null; then
  TEST_CMD="npm test"
elif [[ -f "$PROJECT_DIR/Makefile" ]] && grep -q '^test:' "$PROJECT_DIR/Makefile" 2>/dev/null; then
  TEST_CMD="make test"
elif [[ -f "$PROJECT_DIR/pyproject.toml" ]]; then
  TEST_CMD="pytest"
fi

if [[ -n "$TEST_CMD" ]]; then
  echo "Running tests: $TEST_CMD"
  if ! eval "$TEST_CMD"; then
    echo "BLOCK: Tests failed. Fix test failures before committing."
    exit 2
  fi
fi

echo "Pre-commit quality checks passed."
exit 0
```

### settings.json registration

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [".claude/hooks/pre-commit-quality.sh"]
      }
    ]
  }
}
```

---

## Hook: mcp-health-check

| Field | Value |
|-------|-------|
| Event | `PostToolUseFailure` |
| Tool | — |
| Profile | `standard`, `strict` |
| Purpose | Track MCP tool failures and suggest restarts after repeated issues |

### Template

```bash
#!/usr/bin/env bash
# Hook: mcp-health-check
# Generated for: {{project_name}}
# Profile: {{profile}}
# Logs MCP failures and warns after consecutive issues

set -euo pipefail

HEALTH_FILE=".claude/mcp-health.json"
mkdir -p .claude

TOOL_NAME="${CLAUDE_TOOL_NAME:-unknown}"
ERROR_MSG="${CLAUDE_TOOL_ERROR:-unknown}"
TIMESTAMP=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
EPOCH=$(date +%s)

# Initialize health file if missing
if [[ ! -f "$HEALTH_FILE" ]]; then
  echo '{"failures":[],"last_warning_at":0,"warning_count":0}' > "$HEALTH_FILE"
fi

# Append failure entry
UPDATED=$(jq --arg ts "$TIMESTAMP" --arg tool "$TOOL_NAME" --arg err "$ERROR_MSG" \
  '.failures += [{"timestamp": $ts, "tool": $tool, "error": $err}] | .failures = (.failures | .[-50:])' \
  "$HEALTH_FILE" 2>/dev/null)

if [[ -n "$UPDATED" ]]; then
  echo "$UPDATED" > "$HEALTH_FILE"
fi

# Count consecutive recent failures (last 5 minutes)
CUTOFF=$((EPOCH - 300))
RECENT_COUNT=$(jq --arg cutoff "$CUTOFF" \
  '[.failures[] | select((.timestamp | sub("Z$";"") | split("T") | .[0] + " " + .[1]) as $t | now - 300 < now)] | length' \
  "$HEALTH_FILE" 2>/dev/null || echo "0")

# Fallback: count last N entries
if [[ "$RECENT_COUNT" == "0" ]]; then
  RECENT_COUNT=$(jq '.failures | length' "$HEALTH_FILE" 2>/dev/null || echo "0")
  RECENT_COUNT=$((RECENT_COUNT > 3 ? RECENT_COUNT : 0))
fi

CONSECUTIVE=$(jq '.failures | .[-3:] | length' "$HEALTH_FILE" 2>/dev/null || echo "0")
LAST_WARNING=$(jq -r '.last_warning_at // 0' "$HEALTH_FILE" 2>/dev/null || echo "0")
WARNING_COUNT=$(jq -r '.warning_count // 0' "$HEALTH_FILE" 2>/dev/null || echo "0")

# Determine cooldown: 2min -> 5min -> 10min (exponential)
case "$WARNING_COUNT" in
  0) COOLDOWN=0 ;;
  1) COOLDOWN=120 ;;
  2) COOLDOWN=300 ;;
  *) COOLDOWN=600 ;;
esac

SINCE_LAST=$((EPOCH - LAST_WARNING))

if [[ "$CONSECUTIVE" -ge 3 ]] && [[ "$SINCE_LAST" -ge "$COOLDOWN" ]]; then
  echo "WARNING: MCP tool '$TOOL_NAME' has failed $CONSECUTIVE consecutive times."
  echo "Consider restarting the MCP server: claude mcp restart"
  echo "Recent errors logged to $HEALTH_FILE"

  # Update warning metadata
  jq --arg epoch "$EPOCH" \
    '.last_warning_at = ($epoch | tonumber) | .warning_count += 1' \
    "$HEALTH_FILE" > "$HEALTH_FILE.tmp" && mv "$HEALTH_FILE.tmp" "$HEALTH_FILE"
fi

exit 0
```

### settings.json registration

```json
{
  "hooks": {
    "PostToolUseFailure": [
      {
        "hooks": [".claude/hooks/mcp-health-check.sh"]
      }
    ]
  }
}
```

---

## Generation Instructions

During scaffold mode, hooks are generated based on the detected or selected profile. The generation process follows these steps:

1. **Detect profile** -- Read `ECC_HOOK_PROFILE` from the user's environment. Default to `standard` if unset.

2. **Create hooks directory** -- Ensure `.claude/hooks/` exists in the project root.

3. **Generate hook files** -- For each hook included in the active profile:
   - Copy the template from this reference.
   - Replace `{{project_name}}` with the project's directory name.
   - Replace `{{profile}}` with the active profile level.
   - Set the file as executable (`chmod +x`).

4. **Register in settings.json** -- Merge hook registrations into `.claude/settings.json`. If the file already exists, append to existing hook arrays without overwriting user-defined hooks.

5. **Add profile comment** -- Insert a comment block at the top of each generated hook explaining which profile it belongs to and how to change profiles:

```bash
# Profile: {{profile}}
# To change hook profile: export ECC_HOOK_PROFILE=minimal|standard|strict
# Then re-run scaffold to regenerate hooks.
```

The scaffold mode should never overwrite hooks that have been manually modified by the user. Check file modification timestamps or a `.claude/hooks/.generated` manifest to detect user edits and skip those files during regeneration.
