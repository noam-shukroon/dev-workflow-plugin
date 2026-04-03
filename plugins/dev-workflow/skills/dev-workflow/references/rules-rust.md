# Rust Rules

## Naming Conventions

- **Functions / variables / modules**: `snake_case`
- **Types / traits / enums / enum variants**: `PascalCase`
- **Constants / statics**: `SCREAMING_SNAKE_CASE`
- **Lifetime names**: short and lowercase — `'a`, `'b`, `'ctx`
- **Crate names**: `kebab-case` in `Cargo.toml`, `snake_case` when imported
- **Modules**: one module per file, name matches file name

## Ownership and Borrowing

- Prefer borrowing (`&T`, `&mut T`) over cloning. Clone is a code smell until profiling proves otherwise.
- Use `Cow<'_, str>` (and `Cow` in general) when a function might or might not need to own data.
- Minimize explicit lifetime annotations. Let the compiler elide where it can.
- Prefer owned types (`String`, `Vec<T>`, `PathBuf`) in struct fields. Use references in function parameters.
- Use `Arc<T>` for shared ownership across threads. Avoid `Rc<T>` in async code.
- Prefer `&str` over `&String` and `&[T]` over `&Vec<T>` in function signatures.

## Error Handling

- **Libraries**: use `thiserror` to derive `Error` on custom error enums. Each variant has a descriptive message.
- **Applications**: use `anyhow` for ad-hoc error context. Chain context with `.context("what failed")`.
- Always propagate errors with `?`. Never write manual match-on-Result boilerplate.
- No `unwrap()` in production code. Use `expect("reason")` only where the invariant is provably guaranteed. `unwrap()` is acceptable in tests.
- Implement `Display` for all custom error types. Error messages are lowercase, no trailing punctuation.
- Use `#[non_exhaustive]` on public error enums to allow adding variants without breaking downstream.

## Testing

- **Unit tests**: `#[cfg(test)] mod tests` at the bottom of the source file. Tests live next to the code they test.
- **Integration tests**: in the `tests/` directory. Each file is a separate test crate.
- Use `#[should_panic(expected = "message")]` sparingly — prefer returning `Result` from tests.
- Use **proptest** or **quickcheck** for property-based testing on complex logic (parsers, serializers, algorithms).
- Name tests descriptively: `test_parse_valid_input_returns_expected_struct`, not `test1`.
- Use `assert_eq!` and `assert_ne!` for comparisons (better error messages than `assert!`).
- Use `#[ignore]` for slow tests. Run them explicitly in CI.

## Design Patterns

- **Builder pattern** for types with many optional fields or complex construction.
- **Newtype pattern** (`struct UserId(u64)`) for type safety. Prevents mixing up same-typed arguments.
- **Typestate pattern** for compile-time state machine enforcement (e.g., `Connection<Disconnected>` vs `Connection<Connected>`).
- Prefer enums over trait objects when the set of variants is known. Enums are faster and more ergonomic.
- Use `From`/`Into` traits for type conversions. Implement `From`, get `Into` for free.
- Derive standard traits liberally: `Debug`, `Clone`, `PartialEq`, `Eq`, `Hash` where appropriate.

## Unsafe Code

- Document every `unsafe` block with a `// SAFETY:` comment explaining why the invariants hold.
- Minimize the scope of `unsafe` — wrap it in a safe abstraction as tightly as possible.
- Prefer safe alternatives. Use `unsafe` only when there is no safe way to achieve the required performance or FFI interop.
- Never use `unsafe` to bypass the borrow checker out of convenience. If you're fighting the borrow checker, redesign.
- Audit all `unsafe` code with `miri` where possible.

## Linting and Formatting

- Enable `#![warn(clippy::pedantic)]` in the crate root. Fix or explicitly allow each lint with a reason.
- Enforce `cargo fmt` in CI. No style debates.
- Deny warnings in CI: `RUSTFLAGS="-D warnings"`. Warnings in CI are bugs.
- Use `#![deny(missing_docs)]` for library crates. Every public item gets a doc comment.
- Run `cargo clippy -- -D warnings` in CI alongside `cargo test`.

## General Principles

- Prefer iterators over manual loops. Iterator chains are idiomatic, readable, and often faster.
- Use `Option` and `Result` instead of sentinel values or null-like patterns.
- Prefer `impl Trait` in argument position for simple generic bounds. Use explicit generics when the caller needs to specify the type.
- Use `#[must_use]` on functions where ignoring the return value is almost certainly a bug.
- Feature-gate optional dependencies in `Cargo.toml`. Keep the default feature set minimal.
