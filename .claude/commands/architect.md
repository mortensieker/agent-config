Use the **Architect** sub-agent to design the solution architecture.

The agent will read `spec/REQUIREMENTS.md` and produce one or more spec files (`spec/SPEC.md` for single-component projects, or `spec/SPEC-<NAME>.md` files for multi-component projects).

**Pre-check:** Confirm `spec/REQUIREMENTS.md` exists before starting. If it does not, tell the user to run `/analyst` first.

**Error handling:** If the Architect agent fails to start or returns an internal error, stop immediately, report the failure to the user, and wait for instructions before retrying.
