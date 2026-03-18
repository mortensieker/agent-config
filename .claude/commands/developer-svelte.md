Use the **Developer (SvelteKit)** sub-agent to implement the solution in SvelteKit.

The agent will read `spec/SPEC.md` and implement the solution using SvelteKit best practices and TDD.

**Pre-check:** Confirm `spec/SPEC.md` exists before starting. If it does not, tell the user to run `/architect` first.

**Error handling:** If the SvelteKit Developer agent fails to start or returns an internal error, stop immediately, report the failure to the user, and wait for instructions before retrying. Do not retry silently.
