# agent-config

A versioned Claude Code configuration providing reusable sub-agents for structured software development workflows.

## Contents

```
.claude/
├── CLAUDE.md          # Global instructions loaded by Claude Code
└── agents/            # Sub-agent definitions
    ├── analyst.md
    ├── architect.md
    ├── developer.md
    ├── developer-go.md
    ├── developer-sveltekit.md
    └── reviewer.md
```

## Agents

| Agent | Role |
|---|---|
| Analyst | Elicits and structures requirements into `spec/REQUIREMENTS.md` |
| Architect | Designs the solution and produces `spec/SPEC.md` |
| Developer | Implements code based on `spec/SPEC.md` using TDD |
| Developer (Go) | Go-specific implementation |
| Developer (SvelteKit) | Svelte/SvelteKit-specific implementation |
| Reviewer | Reviews code against `spec/SPEC.md` and produces `spec/COMMENTS.md` |

## Setup via Symlinks

Claude Code reads configuration from `~/.claude/`. Symlink the files from this repo so changes stay version-controlled.

```bash
# Clone the repo
git clone <repo-url> ~/[WORKSPACE_DIR]/agent-config

# Back up any existing files you want to keep
cp ~/.claude/CLAUDE.md ~/.claude/CLAUDE.md.bak   # optional

# Symlink CLAUDE.md
ln -s ~/[WORKSPACE_DIR]/agent-config/.claude/CLAUDE.md ~/.claude/CLAUDE.md

# Symlink the entire agents directory (or individual files)
ln -s ~/[WORKSPACE_DIR]/agent-config/.claude/agents ~/.claude/agents
```

> **Note:** If `~/.claude/agents` already exists as a directory (not a symlink), move or remove it first:
> ```bash
> mv ~/.claude/agents ~/.claude/agents.bak
> ln -s ~/[WORKSPACE_DIR]/agent-config/.claude/agents ~/.claude/agents
> ```

After symlinking, verify with:

```bash
ls -la ~/.claude/CLAUDE.md ~/.claude/agents
```

## Workflow

Agents are designed to be used in sequence within any project:

```
Analyst → Architect → Developer(s) → Reviewer
```

1. **Analyst** — Run to elicit and validate requirements. Produces `spec/REQUIREMENTS.md`.
2. **Architect** — Reads `spec/REQUIREMENTS.md`, designs architecture, produces `spec/SPEC.md`.
3. **Developer** — Reads `spec/SPEC.md`, implements incrementally using TDD.
4. **Reviewer** — Reviews implementation against `spec/SPEC.md`, produces `spec/COMMENTS.md`.

Invoke an agent from any project directory in Claude Code using the Agent tool or the `/agent` slash command.

## Updating

Pull changes and your symlinked config updates automatically:

```bash
cd ~/[WORKSPACE_DIR]/agent-config
git pull
```

## License

MIT
