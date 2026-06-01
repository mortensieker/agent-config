---
name: sveltekit-testing
description: SvelteKit testing patterns with Vitest, Testing Library, and Playwright. Use when writing tests for SvelteKit or Svelte code.
user-invocable: false
---

## TOOLS

| Tool | Purpose |
|---|---|
| Vitest | Unit and integration tests |
| `@testing-library/svelte` | Svelte component tests |
| Playwright | End-to-end tests (when spec requires e2e coverage) |

## TDD WORKFLOW

1. Write the test first — verify it fails before implementing
2. Write the minimum code to make it pass
3. Refactor while keeping tests green

## FILE CONVENTIONS

- Co-locate test files: `foo.test.ts` next to `foo.ts`
- Component tests: `MyComponent.test.ts` next to `MyComponent.svelte`
- E2e tests: `tests/` at project root (Playwright default)

## COMPONENT TESTING

Use `@testing-library/svelte` — test behaviour and rendered output, not implementation details. Use `userEvent` for interactions.

## LOAD FUNCTION & ACTION TESTING

- Test load functions by calling them directly with a mocked `RequestEvent`
- Test form actions with a mocked `Request` — do not spin up a server for unit tests
- Mock `$lib` dependencies with `vi.mock()`

## TEST SCOPE

| Layer | Tool | What to test |
|---|---|---|
| Pure logic | Vitest | Input/output, edge cases |
| Svelte components | Testing Library | Rendered output, interactions |
| Load functions / actions | Vitest + mocks | Data, redirects, errors |
| Critical user flows | Playwright | Full browser flows |
