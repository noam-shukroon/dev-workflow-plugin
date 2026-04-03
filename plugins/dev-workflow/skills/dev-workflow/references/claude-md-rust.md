# Rust CLAUDE.md Template

Detection: `Cargo.toml` present in project root.

Fill in `{{placeholders}}`. Delete unused sections.

```markdown
# CLAUDE.md

**{{project_name}}** — {{one-line description}}.
Built with Rust {{rust_edition}}. Hosted on {{hosting}}.

## Hard Constraints

- **No `unwrap()` or `expect()` in production code.** Use proper error handling with `?` operator. Only `unwrap()` in tests or when the invariant is provably guaranteed (add a comment explaining why).
- **Handle all `Result` and `Option` values.** Never silently discard errors.
- **No `unsafe` without explicit approval.** If `unsafe` is truly needed, document the safety invariant in a `// SAFETY:` comment.
- **Clippy clean.** All code must pass `cargo clippy -- -D warnings` with zero warnings.
- **Production-ready code only.** No placeholders, no TODO-only implementations. Proper error handling, types, and validation.
- {{additional_constraint}}

## North Star Metric

{{the_one_metric_that_matters}}

## Documentation

All detailed docs in `.claude/`. Load only what you need per routing table below.

| File | Contents |
|------|----------|
| `.claude/ARCHITECTURE.md` | Crate structure, module tree, data flow, storage layer |
| `.claude/CONVENTIONS.md` | Naming, error patterns, trait conventions, type aliases |
| `.claude/DECISIONS.md` | Why choices were made, rejected alternatives |
| `.claude/TASKS.md` | Phase checklist, current work, session handoff |
| `.claude/CHANGELOG.md` | Audit trail of doc updates |

### Context routing

| Task involves | Load |
|---------------|------|
| Module structure, new crates, dependencies, storage | ARCHITECTURE.md |
| Naming, patterns, error types, trait design | CONVENTIONS.md |
| Understanding *why* something was built this way | DECISIONS.md |
| Phase progress, what's next, handoff | TASKS.md |

Never load all files speculatively.

### Doc evolution

Update `.claude/` files after tasks that change structure or decisions. Log in CHANGELOG.md.

## Preferences

- Prefer iterators and combinators over manual loops where readability is preserved.
- Use `impl Trait` in argument position for flexibility, concrete types in return position for clarity.
- Derive standard traits (`Debug`, `Clone`, `PartialEq`) on all public types unless there's a reason not to.
- Respond concisely. No filler.
- Ask before assuming when decisions could go multiple ways.

## Commands

\```bash
cargo build                    # Debug build
cargo build --release          # Release build
cargo test                     # Run all tests
cargo clippy -- -D warnings    # Lint with warnings as errors
cargo fmt --check              # Check formatting
cargo doc --no-deps --open     # Generate and view docs
\```

## Pre-Commit Checks

Always run before committing:
\```bash
cargo build                    # Must compile
cargo test                     # Must pass
cargo clippy -- -D warnings    # Must pass
cargo fmt --check              # Must pass
\```
Never commit code that hasn't passed these checks.

## Project Structure

| Path | Purpose |
|------|---------|
| `src/lib.rs` | Library root — public API and module declarations |
| `src/main.rs` | Binary entry point (if applicable) |
| `src/bin/` | Additional binary targets |
| `src/` | Core modules organized by domain |
| `tests/` | Integration tests (each file is a separate test binary) |
| `benches/` | Benchmarks (Criterion or built-in) |
| `examples/` | Runnable examples |

For workspaces:
| Path | Purpose |
|------|---------|
| `crates/` | Workspace member crates, each with its own `Cargo.toml` |
| `Cargo.toml` | Workspace root with `[workspace]` definition |

## Key Patterns

| Pattern | Convention |
|---------|-----------|
| Error handling (libraries) | `thiserror` — define typed errors with `#[derive(Error)]` |
| Error handling (binaries) | `anyhow` — use `anyhow::Result` and `.context("msg")` for wrapping |
| Builder pattern | Use for types with many optional fields. Return `Self` from setters, validate in `build()` |
| Newtype pattern | Wrap primitives for type safety: `struct UserId(Uuid)` |
| Traits | Keep small and focused. Prefer composition of small traits over large ones |
| Serialization | `serde` with `#[derive(Serialize, Deserialize)]`. Use `#[serde(rename_all = "camelCase")]` for JSON APIs |
| Async runtime | `tokio` with `#[tokio::main]` or `#[tokio::test]` |
| Configuration | `config` crate or environment variables via `std::env` |
| Logging | `tracing` for structured, span-based logging |

## Testing

- **Unit tests** in the same file inside `#[cfg(test)] mod tests { ... }`
- **Integration tests** in `tests/` directory, each file is a separate test binary
- **Test helpers** in `tests/common/mod.rs` (shared across integration tests)
- **`#[should_panic]`** for testing expected panics
- **`proptest` or `quickcheck`** for property-based testing when appropriate
- **`assert_eq!` / `assert_ne!`** over plain `assert!` for better error messages
- Mock traits with `mockall` if dependency injection is needed

## Git Workflow

- Default branch: `{{default_branch}}`
- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `chore:`
- Push to the current branch only — never create new branches without explicit approval.
- Never stage `.env*` files, credentials, or secrets.
- Never commit `Cargo.lock` for libraries. Always commit it for binaries.
- Run `git status` + `git branch` before any git operation.
```
