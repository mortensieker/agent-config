Use the **Developer** sub-agent to implement the solution.

The agent will:
1. Detect the technology from the spec and load the appropriate skill(s) from `~/.claude/skills/`
2. Scan `spec/` for all `SPEC*.md` files and confirm which to implement
3. Implement the solution using TDD, following the loaded skill's standards

**Pre-check:** Scan `spec/` for any `SPEC*.md` files before starting. If none exist, tell the user to run `/architect` first. If multiple are found, list them and ask the user which to use.

**Error handling:** If the Developer agent fails to start or returns an internal error, stop immediately, report the failure to the user, and wait for instructions before retrying. Do not retry silently.
