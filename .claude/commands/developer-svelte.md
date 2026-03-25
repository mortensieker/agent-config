Use the **Developer (SvelteKit)** sub-agent to implement the solution in SvelteKit.

The agent will scan `spec/` for all `SPEC*.md` files, confirm which to implement, then implement the solution using SvelteKit best practices and TDD.

**Pre-check:** Scan `spec/` for any `SPEC*.md` files before starting. If none exist, tell the user to run `/architect` first. If multiple are found, list them and ask the user which to use.

**Error handling:** If the SvelteKit Developer agent fails to start or returns an internal error, stop immediately, report the failure to the user, and wait for instructions before retrying. Do not retry silently.
