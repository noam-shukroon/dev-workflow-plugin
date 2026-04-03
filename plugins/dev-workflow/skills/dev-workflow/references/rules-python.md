# Python Rules

## Naming Conventions

- **Functions / variables / modules**: `snake_case`
- **Classes**: `PascalCase`
- **Constants**: `UPPER_SNAKE_CASE`
- **Private / internal**: `_leading_underscore` for module-internal or class-internal names
- **Files / modules**: `snake_case.py`. No hyphens in module names.
- **Dunder methods**: `__init__`, `__repr__`, etc. Never invent custom dunders.

## Type Hints

- All function signatures must have type annotations — parameters and return type.
- Use `from __future__ import annotations` at the top of every module for forward reference support and deferred evaluation.
- Use **Pydantic** for runtime validation of external data (API payloads, config files, user input).
- Define `TypeAlias` for complex types: `type UserMap = dict[str, list[User]]` (3.12+) or `UserMap: TypeAlias = dict[str, list[User]]`.
- Prefer `collections.abc` types for parameters (`Sequence`, `Mapping`, `Iterable`) over concrete types (`list`, `dict`).
- Use `X | None` (3.10+) instead of `Optional[X]`.

## Error Handling

- Catch specific exception types. Never use bare `except:` or `except Exception:` without re-raising.
- Define a project-level base exception (`class AppError(Exception)`) and derive all custom exceptions from it.
- Use `contextlib.suppress(SpecificError)` for expected, ignorable errors instead of try/except/pass.
- Always chain exceptions: `raise NewError("context") from original_error`.
- Log or handle — never both silently. If you catch and log, decide whether to re-raise.
- Use `finally` or context managers (`with`) for cleanup, not manual try/except teardown.

## Testing

- **pytest** over `unittest`. Always.
- Use **fixtures** (`@pytest.fixture`) for setup and teardown. Scope fixtures appropriately (`function`, `module`, `session`).
- Use `@pytest.mark.parametrize` for table-driven tests with multiple inputs.
- Put shared fixtures in `conftest.py` at the appropriate directory level.
- Name test files `test_<module>.py`. Name test functions `test_<behavior_description>`.
- Use `tmp_path` fixture for filesystem tests. Use `monkeypatch` for environment and attribute overrides.
- Mark slow tests with `@pytest.mark.slow` and exclude from default runs.

## Imports

- Sort order: stdlib, then third-party, then local. Enforce with **isort** (or ruff's isort rules).
- Absolute imports preferred: `from myproject.users.service import UserService`.
- Define `__all__` in modules that serve as public API boundaries.
- No wildcard imports (`from x import *`). Ever.
- Avoid circular imports. If two modules need each other, refactor or use deferred imports inside functions.

## Style and Linting

- **Ruff** for linting and formatting. It replaces black, isort, flake8, pyupgrade, and more. One tool, fast.
- **Pyright** (or mypy) for static type checking. Run in CI with strict mode.
- Line length: 88-100 characters. Configure once, never discuss again.
- Docstrings on all public functions and classes. Use Google style or NumPy style — pick one, enforce it.
- No `# type: ignore` without a specific error code and explanation comment.

## Design Patterns

- **Dataclasses** (`@dataclass`) for value objects and plain data containers. Use `frozen=True` for immutability.
- **Protocol** (from `typing`) for structural subtyping — define the shape, not the inheritance tree.
- **ABC** (from `abc`) only when you need shared implementation across subclasses. If there's no shared code, use `Protocol`.
- **Context managers** for any resource that needs setup/teardown (connections, files, locks).
- Prefer composition over inheritance. Flat hierarchies. No deep class trees.

## General Principles

- Use f-strings for string formatting. No `%` formatting, no `.format()` unless deferred formatting is needed.
- Use `pathlib.Path` over `os.path` for filesystem operations.
- Use `enum.Enum` for fixed sets of values. Use `enum.StrEnum` (3.11+) when string compatibility is needed.
- Prefer generators and `itertools` for lazy data processing over materializing large lists.
- Use `logging` module — never `print()` for operational output. Configure logging at the application entry point only.
- Pin dependencies in production (`requirements.txt` or lockfile). Use ranges in libraries (`pyproject.toml`).
