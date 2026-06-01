---
name: sveltekit-project-structure
description: SvelteKit project layout and file conventions. Use when setting up or navigating a SvelteKit project.
user-invocable: false
---

## LANGUAGE & FRAMEWORK

- TypeScript (strict mode) throughout — no plain `.js` files
- SvelteKit (latest stable) with Svelte 5 runes — do NOT use Svelte 4 store patterns in new code
- File-based routing via `src/routes/`

## ROUTING FILE CONVENTIONS

| File | Purpose |
|---|---|
| `+page.svelte` | Page UI component |
| `+page.ts` | Universal load function (server + client) |
| `+page.server.ts` | Server-only load and form actions |
| `+layout.svelte` | Layout UI |
| `+server.ts` | API route handlers |
| `+error.svelte` | Error boundary for the route segment |

Prefer `+page.server.ts` for data that must not be exposed client-side.

## DIRECTORY LAYOUT

```
src/
  routes/           — file-based routes
  lib/
    components/     — reusable Svelte components
    stores/         — shared state (cross-route only)
    utils/          — pure utility functions
    types/          — TypeScript interfaces
  hooks.server.ts   — auth, session, logging
static/             — static assets
```

## PATH ALIASES

- Use `$lib` for all shared code — never `../../` when `$lib` applies

## IMPLEMENTATION STYLE

- Keep `+page.svelte` thin — push logic into `$lib/`
- Use typed `PageData` / `ActionData` from `$types` on every page
- Validate and sanitize all server-side inputs
- Default adapter: `@sveltejs/adapter-auto`; switch to `adapter-node` for self-hosted
