---
name: ship
description: "Build-verify-commit-push workflow with 6-phase pre-flight and git safety guardrails. Use when shipping code: committing, pushing, deploying, or when the user says 'ship it', 'commit and push', 'push to dev/main'."
---

# Ship

Build-verify-commit-push workflow with 6-phase pre-flight and git safety guardrails.

## Pre-flight (6-phase verification)

### Phase 1: Build (blocking)
Run the project's build command (from CLAUDE.md or detect from manifest). Must pass. Stop on failure.

### Phase 2: Type Check (blocking)
Auto-detect and run the appropriate type checker:

| Indicator | Command |
|-----------|---------|
| `tsconfig.json` | `npx tsc --noEmit` |
| `pyrightconfig.json` or `pyproject.toml` | `pyright` |
| `Cargo.toml` | `cargo check` |
| `go.mod` | `go vet ./...` |

Must pass. Stop on failure.

### Phase 3: Lint (blocking)
Auto-detect and run the linter:

| Indicator | Command |
|-----------|---------|
| `.eslintrc*` or `eslint.config.*` | `npx eslint .` |
| `ruff.toml` or `pyproject.toml` with ruff | `ruff check .` |
| `Cargo.toml` | `cargo clippy -- -D warnings` |
| `go.mod` | `golangci-lint run` (if config exists) |

Must pass. Stop on failure.

### Phase 4: Test Suite (blocking)
Run the project's test command. Must pass. Stop on failure.

### Phase 5: Security Scan (advisory)
Quick scan for common security issues:
- Grep for hardcoded secrets: API keys, passwords, tokens in source files (not `.env`)
- Check for `.env` files referenced in code but not in `.gitignore`
- Look for `TODO: security`, `FIXME: auth`, `HACK:` comments
- Check for `eval()`, `innerHTML`, raw SQL string concatenation

Warn on findings but do not block.

### Phase 6: Diff Review (advisory)
- `git diff --stat` to summarize staged changes
- Flag large diffs (>500 lines changed) — suggest splitting
- Flag files that shouldn't change (lockfiles without manifest changes, generated files)
- Summarize what's being shipped in plain language

Warn on findings but do not block.

**On any blocking failure:** Report which phase failed, show the error, and stop. Do not proceed to commit.

## Git Safety

Git safety rules from dev-workflow defaults apply. Additionally:

1. Confirm what will be committed — no `.env*` files staged (unstage if so).
2. If uncommitted changes exist that aren't part of this ship, ask the user before proceeding.

## Commit

1. Stage changes: `git add` specific files (not `-A`). Never stage `.env*`, credentials, or large binaries.
2. Review recent commit messages (`git log --oneline -10`) to match the project's commit style.
3. Write a conventional commit message (`feat:`, `fix:`, `refactor:`, `style:`, `chore:`, `docs:`).
4. Commit.

## Push

1. Push to the current branch's remote tracking branch.
2. If no upstream is set, run `git push -u origin <current-branch>`.
3. Never push to a different branch than the one you're on.

## Post-push

### CI Monitoring
After push, if CI is detectable (`gh run list --branch <current-branch> --limit 1`):
- Wait 60s, check status with `gh run view`.
- If failing: warn user with failing check name and suggest `git revert HEAD`.
- If pending after 60s: report status and move on.

### PR Creation (optional)
Offer to create a pull request:

1. Ask: "Create a PR for this push?"
2. If yes:
   - Generate PR title from conventional commit messages (or most recent commit)
   - Generate PR body from commit log since branch diverged from base
   - Create PR using `gh pr create` with title and body
   - Report PR URL

## Summary

After shipping, report:
```
Shipped to: {{branch}}
Commit: {{hash}} — {{message}}
Files: {{count}} changed (+{{insertions}}/-{{deletions}})
Pre-flight: ✅ build | ✅ types | ✅ lint | ✅ tests | {{⚠️/✅}} security | {{⚠️/✅}} diff
CI: {{status or "not detected"}}
{{PR: url (if created)}}
```
