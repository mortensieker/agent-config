Use the **Reviewer** sub-agent to review the implemented codebase.

The agent will read `spec/SPEC.md` and the current codebase, then produce `spec/COMMENTS.md` with prioritised findings.

**Pre-check:** Confirm both `spec/SPEC.md` and source code files exist before starting. If either is missing, tell the user which step is incomplete.

**Error handling:** If the Reviewer agent fails to start or returns an internal error, stop immediately, report the failure to the user, and wait for instructions before retrying.
