# Go CLAUDE.md Template

Detection: `go.mod` present in project root.

Fill in `{{placeholders}}`. Delete unused sections.

```markdown
# CLAUDE.md

**{{project_name}}** — {{one-line description}}.
Built with Go {{go_version}}. Hosted on {{hosting}}.

## Hard Constraints

- **Always check errors.** Never use `_` to discard errors. Every error must be handled or explicitly wrapped and returned.
- **No bare returns.** Always use named return values or explicit return arguments.
- **Stdlib first.** Prefer standard library over third-party packages. Only add dependencies when stdlib is genuinely insufficient.
- **No `panic` in library code.** Reserve `panic` for truly unrecoverable situations in `main` or init. Return errors instead.
- **Production-ready code only.** No placeholders, no TODO-only implementations. Proper error handling, validation, and logging.
- {{additional_constraint}}

## North Star Metric

{{the_one_metric_that_matters}}

## Documentation

All detailed docs in `.claude/`. Load only what you need per routing table below.

| File | Contents |
|------|----------|
| `.claude/ARCHITECTURE.md` | Package layout, dependency graph, data flow, DB schema |
| `.claude/CONVENTIONS.md` | Naming, error patterns, interface conventions, logging |
| `.claude/DECISIONS.md` | Why choices were made, rejected alternatives |
| `.claude/TASKS.md` | Phase checklist, current work, session handoff |
| `.claude/CHANGELOG.md` | Audit trail of doc updates |

### Context routing

| Task involves | Load |
|---------------|------|
| Package structure, new packages, dependencies, DB | ARCHITECTURE.md |
| Naming, patterns, error handling style, logging | CONVENTIONS.md |
| Understanding *why* something was built this way | DECISIONS.md |
| Phase progress, what's next, handoff | TASKS.md |

Never load all files speculatively.

### Doc evolution

Update `.claude/` files after tasks that change structure or decisions. Log in CHANGELOG.md.

## Preferences

- Keep functions short and focused. If a function exceeds ~40 lines, consider splitting.
- Accept interfaces, return structs.
- Use `context.Context` as the first parameter for any function that does I/O or may be cancelled.
- Respond concisely. No filler.
- Ask before assuming when decisions could go multiple ways.

## Commands

\```bash
go build ./...              # Build all packages
go test ./...               # Run all tests
go test -race ./...         # Run tests with race detector
go vet ./...                # Static analysis
golangci-lint run           # Comprehensive linting
go mod tidy                 # Clean up dependencies
\```

## Pre-Commit Checks

Always run before committing:
\```bash
go build ./...              # Must compile
go test ./...               # Must pass
go vet ./...                # Must pass
golangci-lint run           # Must pass
\```
Never commit code that hasn't passed these checks.

## Project Structure

| Directory | Purpose |
|-----------|---------|
| `cmd/` | Application entry points. Each subdirectory is a binary (`cmd/server/`, `cmd/cli/`) |
| `internal/` | Private packages. Cannot be imported by external modules |
| `pkg/` | Public packages safe for external consumption (use sparingly) |
| `api/` | API definitions — OpenAPI specs, protobuf files, gRPC service definitions |
| `migrations/` | Database migration files |
| `scripts/` | Build and automation scripts |

## Key Patterns

| Pattern | Convention |
|---------|-----------|
| Error wrapping | `fmt.Errorf("operation context: %w", err)` — always add context when wrapping |
| Error types | Define sentinel errors with `var ErrNotFound = errors.New("not found")` |
| Error checking | Use `errors.Is()` and `errors.As()` — never compare error strings |
| Interfaces | Define where consumed, not where implemented. Keep small (1-3 methods) |
| Constructors | `NewXxx(deps) (*Xxx, error)` — validate inputs, return errors |
| Context | First param, never stored in structs: `func (s *Svc) Do(ctx context.Context, ...)` |
| Logging | Structured logging with `slog`. Include request IDs and relevant context |
| Configuration | Environment variables or config files. Never hardcode secrets |
| Concurrency | Prefer channels for coordination. Use `sync.Mutex` only for shared state |

## Testing

- **Table-driven tests** for all functions with multiple input/output cases
- **Test files** co-located: `foo.go` tested in `foo_test.go`
- **Test helpers** use `t.Helper()` to improve failure output
- **Subtests** with `t.Run("case name", ...)` for clear test names
- **Testify** only if already in use — prefer stdlib `testing` package
- **Mocks** via interfaces. Define test doubles in `_test.go` files
- **Integration tests** guarded by build tags: `//go:build integration`

## Git Workflow

- Default branch: `{{default_branch}}`
- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `chore:`
- Push to the current branch only — never create new branches without explicit approval.
- Never stage `.env*` files, credentials, or secrets.
- Run `git status` + `git branch` before any git operation.
```
