---
name: sveltekit-testing
description: SvelteKit testing patterns with Vitest, Testing Library, and Playwright. Use when writing tests for SvelteKit or Svelte code.
user-invocable: false
---

## TEST TOOLS

| Tool | Purpose |
|---|---|
| Vitest | Unit and integration tests |
| `@testing-library/svelte` | Svelte component tests |
| Playwright | End-to-end tests (when spec requires e2e coverage) |

## FILE CONVENTIONS

- Co-locate test files with source: `foo.test.ts` next to `foo.ts`
- Component tests: `MyComponent.test.ts` next to `MyComponent.svelte`
- E2e tests: `tests/` directory at project root (Playwright default)

## TDD WORKFLOW

1. Write the test first — it must fail before any implementation
2. Write the minimum code to make it pass
3. Refactor while keeping tests green

## UNIT TESTING (pure logic)

Test pure `$lib/` functions with Vitest directly:

```typescript
import { describe, it, expect } from 'vitest'
import { formatDate } from '$lib/utils/date'

describe('formatDate', () => {
  it('formats ISO string to display date', () => {
    expect(formatDate('2024-01-15')).toBe('January 15, 2024')
  })
})
```

## COMPONENT TESTING

Use `@testing-library/svelte` for component behaviour tests:

```typescript
import { render, screen } from '@testing-library/svelte'
import { expect, it } from 'vitest'
import UserCard from './UserCard.svelte'

it('displays the user name', () => {
  render(UserCard, { props: { name: 'Alice' } })
  expect(screen.getByText('Alice')).toBeInTheDocument()
})
```

- Test behaviour and rendered output, not implementation details
- Use `userEvent` for simulating interactions

## LOAD FUNCTION & ACTION TESTING

- Test load functions by calling them directly with a mocked `RequestEvent`
- Test form actions with a mocked `Request` — do not spin up a server for unit tests
- Mock `$lib` dependencies with `vi.mock()`

## E2E TESTING (Playwright)

Write Playwright tests for critical user flows only — not for every state:

```typescript
test('user can log in', async ({ page }) => {
  await page.goto('/login')
  await page.fill('[name=email]', 'user@example.com')
  await page.fill('[name=password]', 'password')
  await page.click('button[type=submit]')
  await expect(page).toHaveURL('/dashboard')
})
```

## TEST SCOPE

| Layer | Tool | What to test |
|---|---|---|
| Pure logic | Vitest | Input/output, edge cases |
| Svelte components | Testing Library | Rendered output, user interactions |
| Load functions / actions | Vitest + mocks | Data returned, redirects, errors |
| Critical user flows | Playwright | Full browser flows |

## VALIDATION CHECKLIST

- `npx vitest run` — all Vitest tests pass
- `npx playwright test` — Playwright tests pass (if e2e required)
- No tests that only assert truthiness without checking values
