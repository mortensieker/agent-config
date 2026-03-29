---
name: sveltekit-project-structure
description: SvelteKit project layout and file conventions. Use when setting up or navigating a SvelteKit project.
user-invocable: false
---

## LANGUAGE & RUNTIME

- TypeScript (strict mode) throughout — no plain `.js` files
- Node.js runtime unless the spec states otherwise

## FRAMEWORK

- SvelteKit (latest stable) with Svelte 5 runes (`$state`, `$derived`, `$effect`)
- Do NOT use legacy Svelte 4 store patterns unless the existing codebase requires it
- File-based routing via `src/routes/`

## ROUTING FILE CONVENTIONS

| File | Purpose |
|---|---|
| `+page.svelte` | Page UI component |
| `+page.ts` | Universal load function (runs on server and client) |
| `+page.server.ts` | Server-only load function and form actions |
| `+layout.svelte` | Layout UI component |
| `+layout.ts` / `+layout.server.ts` | Layout load equivalents |
| `+server.ts` | API route handlers (GET, POST, etc.) |
| `+error.svelte` | Error boundary for the route segment |

Prefer `+page.server.ts` for any data that must not be exposed client-side.

## PATH ALIASES

- Use `$lib` for all shared utilities, components, stores, and types (`src/lib/`)
- Never use relative `../../` imports when `$lib` is applicable
- Define additional aliases in `svelte.config.js` if the project warrants it

## DIRECTORY LAYOUT

```
src/
  routes/             — file-based routes
  lib/
    components/       — reusable Svelte components
    stores/           — shared reactive state (if needed across routes)
    utils/            — pure utility functions
    types/            — TypeScript type definitions and interfaces
  app.html            — HTML shell
  hooks.server.ts     — server-side hooks (auth, session, logging)
static/               — static assets served as-is
```

## ADAPTER

- Default to `@sveltejs/adapter-auto`
- Switch to `adapter-node` if the spec requires self-hosted deployment

## STYLING

- Tailwind CSS unless the spec specifies otherwise
- Component-scoped `<style>` blocks for component-specific overrides only
- Do NOT use global CSS for component styling

## IMPLEMENTATION STYLE

- Keep `+page.svelte` files thin — push logic into `$lib/`
- Use typed `PageData` and `ActionData` from `$types` on every page
- Validate and sanitize all server-side inputs
- Protect server routes with auth checks in `hooks.server.ts` or load functions

## BUILD CHECKLIST

- `npx tsc --noEmit` — TypeScript compiles cleanly
- `npx svelte-check` — no Svelte type errors
