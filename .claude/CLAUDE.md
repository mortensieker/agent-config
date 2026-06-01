# Agent Setup

This repository provides reusable Claude Code sub-agents for structured software development workflows.

## Agents

| Agent | Role |
|---|---|
| [Analyst](agents/analyst.md) | Elicits requirements and produces `spec/REQUIREMENTS.md` |
| [Architect](agents/architect.md) | Designs the solution and produces `spec/SPEC.md` or `spec/SPEC-<NAME>.md` |
| [Developer](agents/developer.md) | Implements code based on spec files using TDD |
| [Reviewer](agents/reviewer.md) | Reviews the codebase against spec files and produces `spec/COMMENTS.md` |

## Built-in Skills (complement the agents)

| Skill | When to use |
|---|---|
| `/code-review` | Review the current diff for bugs and quality issues |
| `/security-review` | Deep security scan of pending changes |
| `/fix-comment` | Apply a specific finding from `spec/COMMENTS.md` |

## Skills

Technology standards are defined as composable skills in `skills/`. The Developer agent detects the stack from the spec and loads the relevant skill(s) automatically.

| Skill | Purpose |
|---|---|
| `go` | Bundles all core Go skills |
| `go-project-structure` | Directory layout, modules, interfaces, logging, config |
| `go-error-handling` | Error wrapping, sentinel errors, custom error types |
| `go-concurrency` | Goroutines, channels, context propagation |
| `go-testing` | Table-driven tests, testify, mocking, TDD workflow |
| `go-echo` | Go Echo REST API — extends Go skills with Echo conventions |
| `sveltekit` | Bundles all core SvelteKit skills |
| `sveltekit-project-structure` | Routing conventions, file layout, TypeScript setup |
| `sveltekit-state` | Svelte 5 runes, load functions, shared state |
| `sveltekit-forms` | Form actions, validation, progressive enhancement |
| `sveltekit-testing` | Vitest, Testing Library, Playwright, TDD workflow |

## Workflow

```
Analyst → Architect → Developer → Reviewer
```

1. **Analyst** — Elicits requirements, produces `spec/REQUIREMENTS.md`
2. **Architect** — Reads requirements, designs solution, produces `spec/SPEC*.md`
3. **Developer** — Loads matching skill(s), implements incrementally using TDD
4. **Reviewer** — Reviews against spec, produces `spec/COMMENTS.md`; use `/security-review` and `/code-review` for deeper scans

## Artifacts

| File | Produced by | Consumed by |
|---|---|---|
| `spec/REQUIREMENTS.md` | Analyst | Architect |
| `spec/SPEC.md` or `spec/SPEC-<NAME>.md` | Architect | Developer(s), Reviewer |
| `spec/COMMENTS.md` | Reviewer | Developer(s) via `/fix-comment` |

### Multiple Spec Files

When a project covers more than one component, the Architect produces `spec/SPEC-<NAME>.md` per component. Agents that consume specs MUST scan `spec/` at startup — never assume `spec/SPEC.md` is the only file.

## Error Handling

If a sub-agent call returns an error or empty result: stop, tell the user which agent failed, report the error as-is, and wait for instructions. Do not retry silently.
