Use the **Reviewer** sub-agent to review the implemented codebase.

The agent will scan for spec files, confirm which to review against, then review the codebase and produce `spec/COMMENTS-<repo-name>.md` with prioritised findings.

**Pre-check:** Scan `spec/` for any `SPEC*.md` files before starting. If none exist, tell the user to run `/architect` first. If multiple are found, list them and ask the user which to review against. Also confirm source code files exist — if not, tell the user the developer step is incomplete.

**Error handling:** If the Reviewer agent fails to start or returns an internal error, stop immediately, report the failure to the user, and wait for instructions before retrying.
