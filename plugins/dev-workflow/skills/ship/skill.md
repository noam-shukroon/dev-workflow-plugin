---
name: ship
description: "Build-verify-commit-push workflow with git safety guardrails. Use when shipping code: committing, pushing, deploying, or when the user says 'ship it', 'commit and push', 'push to dev/main'."
---

# Ship

Build-verify-commit-push workflow with git safety guardrails.

## Pre-flight

1. **Build check** — run the project's build command (from CLAUDE.md or detect from `package.json` / manifest). Must pass. Stop if it fails.
2. **Test check** — run the project's test command. Must pass. Stop if it fails.
3. **Lint/type check** — run lint and type-check if available. Warn on failures but don't block.

## Git Safety

Before any git operation:

1. Run `git status` and `git branch` — confirm:
   - Which branch you're on
   - What will be committed
   - No `.env*` files are staged (unstage them if so)
2. **Never create a new branch.** Push to the current branch only.
3. **Never force push.**
4. If uncommitted changes exist that aren't part of this ship, ask the user before proceeding.

## Commit

1. Stage changes: `git add` specific files (not `-A`). Never stage `.env*`, credentials, or large binaries.
2. Review recent commit messages (`git log --oneline -10`) to match the project's commit style.
3. Write a conventional commit message (`feat:`, `fix:`, `refactor:`, `style:`, `chore:`, `docs:`).
4. Commit.

## Push

1. Push to the current branch's remote tracking branch.
2. If no upstream is set, run `git push -u origin <current-branch>`.
3. Never push to a different branch than the one you're on.

## Summary

After pushing, report:
```
Shipped to: {{branch}}
Commit: {{hash}} — {{message}}
Files: {{count}} changed (+{{insertions}}/-{{deletions}})
```
