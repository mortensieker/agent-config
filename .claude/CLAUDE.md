# Agent Setup

This repository provides a set of reusable Claude Code sub-agents for structured software development workflows.

## Agents

| Agent | Role |
|---|---|
| [Analyst](agents/analyst.md) | Elicits and structures requirements into `spec/REQUIREMENTS.md` |
| [Architect](agents/architect.md) | Designs the solution and produces `spec/SPEC.md` or `spec/SPEC-<NAME>.md` files |
| [Developer](agents/developer.md) | Implements code based on one or more spec files using TDD |
| [Go Developer](agents/developer-go.md) | Go-specific implementation |
| [Svelte Developer](agents/developer-sveltekit.md) | Svelte/SvelteKit-specific implementation |
| [Reviewer](agents/reviewer.md) | Reviews code against one or more spec files and produces `spec/COMMENTS-<repo-name>.md` |

## Workflow

The agents are designed to be used in sequence:

```
Analyst → Architect → Developer(s) → Reviewer
```

1. **Analyst** — Engages with the user to elicit and validate requirements. Produces `spec/REQUIREMENTS.md`.
2. **Architect** — Reads `spec/REQUIREMENTS.md`, designs the solution architecture, produces `spec/SPEC.md`. For multi-component projects, produces separate `spec/SPEC-<NAME>.md` files (e.g. `spec/SPEC-API.md`, `spec/SPEC-CLIENT.md`).
3. **Developer / Go Developer / Svelte Developer** — Scans for spec files, asks which to use if multiple exist, then implements incrementally using TDD.
4. **Reviewer** — Scans for spec files, asks which to use if multiple exist, then reviews the codebase and produces `spec/COMMENTS-<repo-name>.md`.

## Artifacts

| File | Produced by | Consumed by |
|---|---|---|
| `spec/REQUIREMENTS.md` | Analyst | Architect |
| `spec/SPEC.md` or `spec/SPEC-<NAME>.md` | Architect | Developer(s), Reviewer |
| `spec/COMMENTS-<repo-name>.md` | Reviewer | Developer(s) |

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
