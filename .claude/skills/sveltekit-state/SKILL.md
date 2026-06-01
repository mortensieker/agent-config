---
name: sveltekit-state
description: SvelteKit state management and data fetching patterns using Svelte 5 runes and load functions. Use when managing component state or fetching data in SvelteKit.
user-invocable: false
---

## SVELTE 5 RUNES

| Rune | Purpose |
|---|---|
| `$state` | Reactive local state |
| `$derived` | Computed values from state |
| `$effect` | Side effects when state changes |
| `$props` | Typed component props |
| `$bindable` | Two-way bindable prop |

Do NOT use Svelte 4 `writable`/`readable` stores in new code.

## STATE RULES

- Prefer component-local `$state` — don't reach for global state unless the spec requires it
- Shared state (`$lib/stores/`) only for state that genuinely persists across route navigations

## DATA FETCHING

Always use SvelteKit `load` functions — do NOT fetch inside components:

```typescript
// +page.server.ts
export const load: PageServerLoad = async ({ params, locals }) => {
  const item = await locals.db.getItem(params.id)
  if (!item) error(404, 'Not found')
  return { item }
}
```

```svelte
<script lang="ts">
  let { data }: { data: PageData } = $props()
</script>
```

## LOAD FUNCTION RULES

- `+page.server.ts` for DB queries, secrets, server-side auth
- `+page.ts` for public API calls that can run on client and server
- Never expose raw DB models to the client — map to DTOs in the load function
- Use `error(status, message)` from `@sveltejs/kit` for expected HTTP errors
- Provide `+error.svelte` at the appropriate route level
