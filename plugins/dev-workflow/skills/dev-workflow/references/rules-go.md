# Go Rules

## Naming Conventions

- **Exported identifiers**: `PascalCase` (functions, types, constants, variables)
- **Unexported identifiers**: `camelCase`
- **Receiver names**: 1-2 characters, consistent across methods (e.g., `s` for `Server`, `db` for `Database`)
- **Acronyms**: all-caps — `HTTP`, `ID`, `URL`, `API`, `JSON`, `SQL` (not `Http`, `Id`, `Url`)
- **Packages**: short, lowercase, single-word. No underscores, no mixedCaps. Name reflects purpose, not contents.
- **Files**: `snake_case.go`. Test files: `snake_case_test.go`.

## Error Handling

- Always check returned errors. Never assign to `_` unless you document why.
- Wrap errors with context: `fmt.Errorf("failed to fetch user %d: %w", id, err)`. Always use `%w` for wrappable errors.
- Define custom error types for domain errors. Implement the `error` interface.
- Use `errors.Is()` and `errors.As()` for error inspection. Never compare error strings.
- Never `panic()` in library code. Reserve panic for truly unrecoverable states in `main` or init.
- Return early on error — avoid deep nesting. The happy path stays left-aligned.

## Package Design

- Small, focused packages. One clear responsibility per package.
- No circular dependencies. If two packages need each other, extract a shared interface or third package.
- Use `internal/` for packages that must not be imported by external consumers.
- Avoid `package utils`, `package common`, `package helpers` — name packages by what they do, not how they're used.
- `cmd/` for entry points, `internal/` for private logic, top-level for public API.

## Testing

- **Table-driven tests** are the default pattern. Use a slice of test cases with descriptive names.
- Use `t.Run()` for subtests — enables targeted test execution and clear failure output.
- Call `t.Helper()` in test helper functions so failures report the caller's line.
- Store test fixtures in `testdata/` directory (ignored by the Go tool).
- Test files live in the same package (`_test.go` suffix). Use `_test` package suffix only for black-box tests.
- No assertion libraries required — `if got != want { t.Errorf(...) }` is idiomatic. If you use one, `testify` is standard.
- Use `t.Parallel()` for tests that can run concurrently.

## Concurrency

- Communicate via channels. Don't share memory — share by communicating.
- Protect shared state with `sync.Mutex` or `sync.RWMutex` when channels are overkill.
- Use `context.Context` as the first parameter for cancellation, timeouts, and request-scoped values.
- Always select on `ctx.Done()` in long-running goroutines.
- Use `sync.WaitGroup` or `errgroup.Group` to wait for goroutine completion.
- Never launch a goroutine without a clear shutdown path. Leaked goroutines are bugs.

## Interfaces

- Accept interfaces, return structs. Callers define the interfaces they need.
- Keep interfaces small — 1 to 3 methods. Prefer composition of small interfaces over large ones.
- Define interfaces at the consumer, not the producer. A package should not export interfaces that only it implements.
- The standard library interfaces (`io.Reader`, `io.Writer`, `fmt.Stringer`) are your model. Follow them.

## Style and Linting

- `gofmt` is law. Non-negotiable. Run on save.
- Use `golangci-lint` with a meaningful configuration (not just defaults). Recommended linters: `errcheck`, `govet`, `staticcheck`, `gosimple`, `ineffassign`, `revive`.
- Write godoc comments on all exported symbols. Start with the identifier name: `// Server handles incoming HTTP requests.`
- Avoid `init()` functions. They make code harder to test and reason about.
- Use `go vet` in CI. It catches real bugs that compile fine.

## General Principles

- Prefer value receivers for small structs. Use pointer receivers when the method mutates state or the struct is large.
- Use `defer` for cleanup (closing files, unlocking mutexes). Understand that deferred calls run LIFO.
- Avoid package-level variables. Pass dependencies explicitly.
- Use `//go:embed` for static assets. No external file-loading at runtime when embed works.
- Prefer `struct{}` for signal channels and set implementations (zero allocation).
