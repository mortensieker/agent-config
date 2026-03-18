Use the **Go Developer** sub-agent to implement the solution in Go.

The agent will read `spec/SPEC.md` and implement the solution using Go best practices and TDD.

**Pre-check:** Confirm `spec/SPEC.md` exists before starting. If it does not, tell the user to run `/architect` first.

**Error handling:** If the Go Developer agent fails to start or returns an internal error, stop immediately, report the failure to the user, and wait for instructions before retrying. Do not retry silently.
