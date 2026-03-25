# Fix Review Comment

Implement a fix for a review finding from `spec/COMMENTS-<repo-name>.md`.

## Arguments

$ARGUMENTS — The finding key (e.g., "H1", "M3", "L2") or a keyword to search for.

## Instructions

1. Determine the comments file:
   - Check the current working directory name, or run `git rev-parse --show-toplevel` and take the basename, to get the repo name.
   - Look for `spec/COMMENTS-<repo-name>.md`. If no such file exists, glob `spec/COMMENTS-*.md` and use what is found (if ambiguous, ask the user).
2. Read that file and locate the finding matching the argument.
   - If a key like `H1` or `M3` is given, match it directly.
   - If a keyword is given, search finding titles and descriptions. If ambiguous, list candidates and ask which one.
2. If the finding's Status is already `Fixed` or `Skipped` in the findings index, tell the user and stop.
3. Summarize what the finding requires and which files are involved.
4. Propose your implementation approach in bullet points. **Wait for the user's approval before making any code changes.**
5. After approval, implement the fix ONE change at a time.
6. Run the project's test suite and ensure all tests pass. If tests fail, fix them before continuing.
7. Update the comments file (`spec/COMMENTS-<repo-name>.md`):
   - Change the finding's Status in the findings index table from `Open` to `Fixed (<date>)`.
   - Add a `**Resolved:**` line to the finding's section with a brief note of what was changed.
8. Show a summary of all files changed.

## Rules

- Do NOT commit or stage any git changes.
- Do NOT proceed to the next finding — only fix the one specified.
- Do NOT make changes beyond what the finding requires.
- If the fix requires a spec update (e.g., SPEC-API.md), include that in your proposal.
