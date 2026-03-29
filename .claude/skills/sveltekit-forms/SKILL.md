---
name: sveltekit-forms
description: SvelteKit form actions and validation patterns. Use when implementing forms, mutations, or server-side data submission in SvelteKit.
user-invocable: false
---

## FORM ACTIONS

Use SvelteKit form actions in `+page.server.ts` for all mutations — do not call API routes from the client for form submissions:

```typescript
// +page.server.ts
import { fail, redirect } from '@sveltejs/kit'
import type { Actions } from './$types'

export const actions: Actions = {
  create: async ({ request, locals }) => {
    const data = await request.formData()
    const name = data.get('name')

    if (!name || typeof name !== 'string') {
      return fail(400, { name, missing: true })
    }

    await locals.db.createItem({ name })
    redirect(303, '/items')
  }
}
```

## PROGRESSIVE ENHANCEMENT

Always use `use:enhance` on forms — the app must work without JavaScript:

```svelte
<form method="POST" action="?/create" use:enhance>
  <input name="name" />
  <button type="submit">Create</button>
</form>
```

## VALIDATION

- Validate all inputs server-side in the action, regardless of client-side validation
- Return `fail(400, { field, error })` for validation errors — never throw
- Type-narrow all `FormData` values before use (`typeof value === 'string'`)
- Use `zod` or a validation library for complex schemas — do not hand-roll validation for multi-field forms

## ERROR FEEDBACK

Use `ActionData` returned from `fail()` to display errors in the page:

```svelte
<script lang="ts">
  import type { ActionData } from './$types'
  let { form }: { form: ActionData } = $props()
</script>

{#if form?.missing}
  <p class="error">Name is required</p>
{/if}
```

## FILE UPLOADS

- Use `request.formData()` with `instanceof File` check
- Stream to storage — never buffer the entire file in memory
- Validate MIME type and size server-side before processing

## CSRF PROTECTION

- SvelteKit's form actions are CSRF-protected by default for same-origin forms
- If exposing actions to cross-origin clients, add explicit CSRF token validation

## CHECKLIST

- All form inputs validated server-side
- Validation errors returned via `fail()`, not thrown
- `use:enhance` applied to all forms
- No client-side fetch calls that duplicate form action logic
