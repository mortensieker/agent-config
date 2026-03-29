# Agent Setup

This repository provides a set of reusable Claude Code sub-agents for structured software development workflows.

## Agents

| Agent | Role |
|---|---|
| [Analyst](agents/analyst.md) | Elicits and structures requirements into `spec/REQUIREMENTS.md` |
| [Architect](agents/architect.md) | Designs the solution and produces `spec/SPEC.md` or `spec/SPEC-<NAME>.md` files |
| [Developer](agents/developer.md) | Implements code based on one or more spec files using TDD; auto-loads the appropriate technology skill |
| [Reviewer](agents/reviewer.md) | Reviews code against one or more spec files and produces `spec/COMMENTS.md` |

## Skills

Technology standards are defined as composable skills in `skills/`. The Developer agent detects the stack from the spec and loads the relevant skill(s) automatically.

| Skill | Purpose |
|---|---|
| `go` | Bundles all core Go skills (project structure, error handling, concurrency, testing) |
| `go-project-structure` | Go directory layout, modules, interfaces, logging, config |
| `go-error-handling` | Error wrapping, sentinel errors, custom error types |
| `go-concurrency` | Goroutines, channels, context propagation |
| `go-testing` | Table-driven tests, testify, mocking, TDD workflow |
| `go-echo` | Go Echo REST API — extends all Go skills with Echo-specific conventions |
| `sveltekit` | Bundles all core SvelteKit skills |
| `sveltekit-project-structure` | Routing conventions, file layout, TypeScript setup |
| `sveltekit-state` | Svelte 5 runes, load functions, shared state, data fetching |
| `sveltekit-forms` | Form actions, validation, progressive enhancement |
| `sveltekit-testing` | Vitest, Testing Library, Playwright, TDD workflow |

## Workflow

The agents are designed to be used in sequence:

```
Analyst → Architect → Developer → Reviewer
```

1. **Analyst** — Engages with the user to elicit and validate requirements. Produces `spec/REQUIREMENTS.md`.
2. **Architect** — Reads `spec/REQUIREMENTS.md`, designs the solution architecture, produces `spec/SPEC.md`. For multi-component projects, produces separate `spec/SPEC-<NAME>.md` files (e.g. `spec/SPEC-API.md`, `spec/SPEC-CLIENT.md`).
3. **Developer** — Detects technology from the spec, loads the matching skill(s), scans for spec files, asks which to use if multiple exist, then implements incrementally using TDD.
4. **Reviewer** — Scans for spec files, asks which to use if multiple exist, then reviews the codebase and produces `spec/COMMENTS.md`.

## Artifacts

| File | Produced by | Consumed by |
|---|---|---|
| `spec/REQUIREMENTS.md` | Analyst | Architect |
| `spec/SPEC.md` or `spec/SPEC-<NAME>.md` | Architect | Developer(s), Reviewer |
| `spec/COMMENTS.md` | Reviewer | Developer(s) |

### Multiple Spec Files

When a project covers more than one distinct component (e.g. a client and an API), the Architect produces separate spec files named `spec/SPEC-<NAME>.md` rather than a single `spec/SPEC.md`.

Agents that consume a spec (Developer variants, Reviewer) MUST scan the `spec/` directory at startup:
- If exactly one spec file is found, use it automatically.
- If multiple spec files are found, list them and ask the user which to use, or whether to work across all of them.
- Never silently assume `spec/SPEC.md` is the only file.

## Error Handling Rules

These rules apply whenever you invoke a sub-agent via the Task tool.

**If a Task tool call returns an internal error, an empty result, or `[Tool result missing due to internal error]`:**
- Stop immediately. Do not retry silently.
- Tell the user exactly which agent failed (e.g. "The Developer agent failed to start").
- Report the error as-is so the user can see it.
- Wait for the user to decide whether to retry or investigate.

**Do not:**
- Silently retry a failed agent call
- Continue the workflow after an agent failure
- Burn tokens attempting workarounds without telling the user first
