# TypeScript Rules

## Naming Conventions

- **Functions / variables**: `camelCase`
- **Types / classes / components**: `PascalCase`
- **Constants**: `UPPER_SNAKE_CASE`
- **Files**: `kebab-case.ts` (e.g., `user-service.ts`, `auth-provider.tsx`)
- **Test files**: `kebab-case.test.ts` alongside source files

## Strict Mode

- `strict: true` in `tsconfig.json` is non-negotiable. Every project, every time.
- Never use `any`. Use `unknown` and narrow with type guards.
- Never use `@ts-ignore`. Use `@ts-expect-error` with a reason comment explaining why the suppression is necessary.
- Enable `noUncheckedIndexedAccess` for safer array/object access.
- Enable `exactOptionalPropertyTypes` when feasible.

## Error Handling

- Define typed error classes or discriminated unions for domain errors.
- Use the Result pattern (`{ ok: true, data: T } | { ok: false, error: E }`) for operations that can fail expectedly. Reserve exceptions for truly exceptional cases.
- Never swallow errors silently. Every `catch` block must log, rethrow, or return a typed error.
- Prefer `cause` chaining (`new Error("context", { cause: original })`) for error wrapping.

## Testing

- Structure: `describe` / `it` blocks with arrange-act-assert pattern.
- Prefer **Vitest** over Jest for new projects (faster, native ESM, compatible API).
- Mock external dependencies only (network, filesystem, third-party services). Never mock the unit under test.
- Use `vi.fn()` / `vi.spyOn()` for mocks. Reset between tests with `beforeEach`.
- Colocate test files with source: `user-service.ts` + `user-service.test.ts`.
- Aim for behavior-driven tests, not implementation-detail tests.

## Imports

- Named imports preferred. Avoid default exports except for framework conventions (React components, Next.js pages).
- Use barrel exports (`index.ts`) for public API boundaries only. Do not barrel internal modules.
- Configure path aliases (`@/`) over deep relative imports (`../../../`).
- Sort: external deps first, then internal aliases, then relative imports. Enforce with ESLint or Prettier plugin.

## Async Patterns

- Always `await` promises. No floating promises — they hide errors and break control flow.
- Use `Promise.all()` for independent parallel operations. Use `Promise.allSettled()` when partial failure is acceptable.
- Use `AbortController` for cancellable async operations (fetch, long-running tasks).
- Prefer `async/await` over `.then()` chains. Never mix the two in the same function.
- Set timeouts on all external calls. Never trust a third-party to respond promptly.

## Linting and Formatting

- **ESLint** with strict config (`@typescript-eslint/strict-type-checked` + `@typescript-eslint/stylistic-type-checked`).
- **Prettier** for formatting. No manual formatting debates.
- Enforce in CI — code that doesn't lint doesn't merge.
- No `eslint-disable` without an accompanying comment explaining why.
- Recommended additional rules: `no-console` (use a logger), `no-restricted-imports`, `consistent-type-imports`.

## General Principles

- Prefer `interface` for object shapes that may be extended. Use `type` for unions, intersections, and utility types.
- Use `as const` for literal constants and discriminated unions.
- Prefer `Map`/`Set` over plain objects when keys are dynamic.
- Use `readonly` on properties and parameters that should not be mutated.
- Never use `enum`. Use `as const` objects or string literal unions instead.
