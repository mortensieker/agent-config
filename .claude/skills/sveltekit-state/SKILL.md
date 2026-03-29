---
name: sveltekit-state
description: SvelteKit state management and data fetching patterns using Svelte 5 runes and load functions. Use when managing component state or fetching data in SvelteKit.
user-invocable: false
---

## SVELTE 5 RUNES

Use runes for all reactive state — do NOT use Svelte 4 `writable`/`readable` stores in new code:

```svelte
<script lang="ts">
  let count = $state(0)
  let doubled = $derived(count * 2)

  $effect(() => {
    console.log('count changed:', count)
  })
</script>
```

| Rune | Purpose |
|---|---|
| `$state` | Reactive local state |
| `$derived` | Computed values derived from state |
| `$effect` | Side effects that run when state changes |
| `$props` | Typed component props |
| `$bindable` | Two-way bindable prop |

## COMPONENT STATE

- Prefer component-local `$state` — don't reach for global state unless the spec requires it
- Keep state as close to where it's used as possible
- Extract reusable stateful logic into `$lib/` as plain TypeScript functions or classes

## SHARED STATE ACROSS ROUTES

Use `$lib/stores/` only for state that genuinely needs to persist across route navigations (e.g. shopping cart, user preferences):

```typescript
// src/lib/stores/cart.svelte.ts
export const cart = $state<CartItem[]>([])
```

Avoid global mutable state unless the spec explicitly requires it.

## DATA FETCHING

Always use SvelteKit `load` functions — do NOT fetch data directly inside components:

```typescript
// +page.server.ts
import type { PageServerLoad } from './$types'

export const load: PageServerLoad = async ({ params, locals }) => {
  const item = await locals.db.getItem(params.id)
  if (!item) error(404, 'Not found')
  return { item }
}
```

```svelte
<!-- +page.svelte -->
<script lang="ts">
  import type { PageData } from './$types'
  let { data }: { data: PageData } = $props()
</script>
```

## LOAD FUNCTION RULES

- Use `+page.server.ts` for data that must stay server-side (DB queries, secrets)
- Use `+page.ts` for data that can be shared on client and server (public API calls)
- Use `locals` and `hooks.server.ts` for auth context and session management
- Never expose raw DB models to the client — map to DTOs in the load function

## ERROR HANDLING

- Use `error(status, message)` from `@sveltejs/kit` for expected HTTP errors
- Provide `+error.svelte` at the appropriate route level to display error pages
- Never swallow errors silently in load functions

## CHECKLIST

- No `fetch` calls inside `+page.svelte` — all data flows through load functions
- `PageData` and `ActionData` types imported from `$types` on every page
- Shared state justified by the spec, not convenience
- `+error.svelte` pages present for routes with fallible load functions
