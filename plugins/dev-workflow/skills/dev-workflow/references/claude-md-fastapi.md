# FastAPI CLAUDE.md Template

Detection: `pyproject.toml` with FastAPI as a dependency.

Fill in `{{placeholders}}`. Delete unused sections.

```markdown
# CLAUDE.md

**{{project_name}}** — {{one-line description}}.
Built with FastAPI, Python {{python_version}}, Pydantic v2. Hosted on {{hosting}}.

## Hard Constraints

- **Type hints everywhere.** All function signatures, variables, and return types must be annotated. No `Any` unless genuinely needed.
- **Pydantic models for all request/response bodies.** Never use raw dicts for API input or output. Define schemas explicitly.
- **Async by default.** Use `async def` for all route handlers and service functions. Only use sync for CPU-bound operations.
- **No `*` imports.** Always import explicitly.
- **Production-ready code only.** No placeholders, no TODO-only implementations. Proper error handling, validation, and logging.
- {{additional_constraint}}

## North Star Metric

{{the_one_metric_that_matters}}

## Documentation

All detailed docs in `.claude/`. Load only what you need per routing table below.

| File | Contents |
|------|----------|
| `.claude/ARCHITECTURE.md` | App structure, route map, data flow, DB schema |
| `.claude/CONVENTIONS.md` | Naming, patterns, error handling, logging conventions |
| `.claude/DECISIONS.md` | Why choices were made, rejected alternatives |
| `.claude/TASKS.md` | Phase checklist, current work, session handoff |
| `.claude/CHANGELOG.md` | Audit trail of doc updates |

### Context routing

| Task involves | Load |
|---------------|------|
| App structure, new routes, dependencies, DB | ARCHITECTURE.md |
| Naming, patterns, error handling, type conventions | CONVENTIONS.md |
| Understanding *why* something was built this way | DECISIONS.md |
| Phase progress, what's next, handoff | TASKS.md |

Never load all files speculatively.

### Doc evolution

Update `.claude/` files after tasks that change structure or decisions. Log in CHANGELOG.md.

## Preferences

- Keep route handlers thin. Business logic belongs in service functions.
- One router per domain area. Group related endpoints together.
- Use dependency injection for database sessions, auth, and shared services.
- Respond concisely. No filler.
- Ask before assuming when decisions could go multiple ways.

## Commands

\```bash
uv run uvicorn app.main:app --reload     # Dev server (http://localhost:8000)
uv run pytest                             # Run all tests
uv run pytest -x -v                       # Run tests, stop on first failure
uv run ruff check .                       # Lint
uv run ruff format .                      # Format
uv run pyright                            # Type check
uv run alembic upgrade head               # Run DB migrations
\```

## Pre-Commit Checks

Always run before committing:
\```bash
uv run ruff check .       # Must pass
uv run pyright             # Must pass
uv run pytest              # Must pass
\```
Never commit code that hasn't passed these checks.

## Project Structure

| Directory | Purpose |
|-----------|---------|
| `app/` | Application root — contains `main.py` with FastAPI app instance |
| `app/routers/` | Route handlers grouped by domain (`users.py`, `items.py`, etc.) |
| `app/models/` | SQLAlchemy / ORM models (database tables) |
| `app/schemas/` | Pydantic models for request/response validation |
| `app/services/` | Business logic layer — called by routers, calls models |
| `app/dependencies/` | FastAPI dependency injection functions |
| `app/core/` | Configuration, security, shared utilities |
| `alembic/` | Database migrations |
| `tests/` | Test files mirroring `app/` structure |

## Key Patterns

| Pattern | Convention |
|---------|-----------|
| Dependency injection | Use `Depends()` for DB sessions, auth, services. Define in `app/dependencies/` |
| Request validation | Pydantic `BaseModel` subclass with field validators. Use `Field()` for constraints |
| Response models | Always set `response_model=` on routes. Use separate Create/Read/Update schemas |
| Error handling | Raise `HTTPException` in routers. Use custom exception handlers for domain errors |
| Settings | `pydantic-settings` with `BaseSettings` class. Load from env vars with `.env` support |
| Database sessions | Async sessions via `Depends(get_db)`. One session per request lifecycle |
| Authentication | Dependency-based: `Depends(get_current_user)` on protected routes |
| Background tasks | `BackgroundTasks` for fire-and-forget work. Use Celery/ARQ for heavy jobs |
| Logging | Structured logging with `structlog`. Include request IDs via middleware |
| Middleware | CORS, request ID injection, timing. Register in `app/main.py` |

## Testing

- **Framework:** pytest with `pytest-asyncio` for async tests
- **Client:** `httpx.AsyncClient` with `ASGITransport` for testing routes
- **Fixtures:** Shared fixtures in `tests/conftest.py` — app instance, test client, test DB
- **Database:** Use a separate test database. Reset between tests with transactions
- **Structure:** Mirror `app/` layout — `tests/test_routers/`, `tests/test_services/`
- **Factories:** Use `factory_boy` or simple factory functions for test data
- **Coverage:** `uv run pytest --cov=app --cov-report=term-missing`

## Git Workflow

- Default branch: `{{default_branch}}`
- Conventional commits: `feat:`, `fix:`, `refactor:`, `docs:`, `chore:`
- Push to the current branch only — never create new branches without explicit approval.
- Never stage `.env*` files, credentials, or secrets.
- Run `git status` + `git branch` before any git operation.
```
