# Agent Setup

This repository provides a set of reusable Claude Code sub-agents for structured software development workflows.

## Agents

| Agent | Role |
|---|---|
| [Analyst](agents/analyst.md) | Elicits and structures requirements into `spec/REQUIREMENTS.md` |
| [Architect](agents/architect.md) | Designs the solution and produces `spec/SPEC.md` |
| [Developer](agents/developer.md) | Implements code based on `spec/SPEC.md` using TDD |
| [Go Developer](agents/go-developer.md) | Go-specific implementation |
| [Svelte Developer](agents/svelte-developer.md) | Svelte/SvelteKit-specific implementation |
| [Reviewer](agents/reviewer.md) | Reviews code against `spec/SPEC.md` and produces `spec/COMMENTS.md` |

## Workflow

The agents are designed to be used in sequence:

```
Analyst → Architect → Developer(s) → Reviewer
```

1. **Analyst** — Engages with the user to elicit and validate requirements. Produces `spec/REQUIREMENTS.md`.
2. **Architect** — Reads `spec/REQUIREMENTS.md`, designs the solution architecture, produces `spec/SPEC.md`.
3. **Developer / Go Developer / Svelte Developer** — Reads `spec/SPEC.md`, implements the solution incrementally using TDD.
4. **Reviewer** — Reviews the implemented codebase against `spec/SPEC.md`. Produces `spec/COMMENTS.md`.

## Artifacts

| File | Produced by | Consumed by |
|---|---|---|
| `spec/REQUIREMENTS.md` | Analyst | Architect |
| `spec/SPEC.md` | Architect | Developer(s), Reviewer |
| `spec/COMMENTS.md` | Reviewer | Developer(s) |
