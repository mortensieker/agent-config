Use the **Developer** sub-agent to implement the solution.

The agent will read `spec/SPEC.md` and implement the solution using TDD.

**Pre-check:** Confirm `spec/SPEC.md` exists before starting. If it does not, tell the user to run `/architect` first.

**Error handling:** If the Developer agent fails to start or returns an internal error, stop immediately, report the failure to the user, and wait for instructions before retrying. Do not retry silently.
