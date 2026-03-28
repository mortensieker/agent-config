Check for dependency updates in the current project. For any major or minor version updates found, create a new git branch, apply the updates, and run the test suite.

## Steps

### 1. Detect package manager

Look for the following files in the project root to determine the package manager(s) in use:

| File | Package manager |
|---|---|
| `package.json` | npm / yarn / pnpm |
| `yarn.lock` | yarn |
| `pnpm-lock.yaml` | pnpm |
| `go.mod` | Go modules |
| `requirements.txt` / `pyproject.toml` / `Pipfile` | Python pip / pipenv / poetry |
| `Gemfile` | Ruby bundler |
| `Cargo.toml` | Rust cargo |
| `composer.json` | PHP composer |

If no recognised package manager files are found, tell the user and stop.

### 2. Check for outdated dependencies

Run the appropriate outdated-check command for each detected package manager:

- **npm**: `npm outdated --json`
- **yarn (classic)**: `yarn outdated --json`
- **yarn (berry)**: `yarn upgrade-interactive` (non-interactive: `yarn up --dry-run` or parse `yarn info`)
- **pnpm**: `pnpm outdated --format json`
- **Go**: `go list -u -m -json all`
- **pip**: `pip list --outdated --format json`
- **poetry**: `poetry show --outdated`
- **bundler**: `bundle outdated`
- **cargo**: `cargo outdated -R`
- **composer**: `composer outdated`

### 3. Filter to major and minor updates only

From the output, identify packages where the available version differs in the **major** or **minor** segment from the currently installed version. Ignore patch-only updates.

If no major or minor updates are found, report this to the user and stop — no branch or changes are needed.

### 4. Summarise and confirm

Print a clear table of the packages to be updated, showing:
- Package name
- Current version
- New version
- Update type (major / minor)

Ask the user to confirm before proceeding.

### 5. Create a new git branch

Create a branch named:

```
deps/update-YYYY-MM-DD
```

using today's date. If that branch already exists, append a counter: `deps/update-YYYY-MM-DD-2`, etc.

```bash
git checkout -b deps/update-YYYY-MM-DD
```

### 6. Apply the updates

Update only the packages identified in step 3. Use the least-invasive update command for the package manager:

- **npm**: `npm install <pkg>@latest` for each package (or `npm update` if all are being updated)
- **yarn**: `yarn upgrade <pkg>@latest`
- **pnpm**: `pnpm update <pkg>@latest`
- **Go**: `go get <module>@latest` for each, then `go mod tidy`
- **pip**: `pip install --upgrade <pkg>` for each
- **poetry**: `poetry add <pkg>@latest`
- **bundler**: `bundle update <gem>`
- **cargo**: update version in `Cargo.toml`, then `cargo update`
- **composer**: `composer require <pkg>:^<new-major>`

### 7. Run the test suite

Detect and run the project's tests:

- **npm/yarn/pnpm**: look for a `test` script in `package.json` → `npm test` / `yarn test` / `pnpm test`
- **Go**: `go test ./...`
- **Python**: try `pytest`, then `python -m pytest`, then `python -m unittest discover`
- **Ruby**: `bundle exec rspec` or `bundle exec rake test`
- **Rust**: `cargo test`
- **PHP**: `./vendor/bin/phpunit`

If no test command can be determined, warn the user and skip this step.

### 8. Report results

After running tests:

- **Tests pass**: Commit the changes with message `chore: update major/minor dependencies (YYYY-MM-DD)` and report success. List the updated packages in the commit body.
- **Tests fail**: Report the failure output. Do **not** commit. Ask the user how they'd like to proceed (fix failures, revert specific packages, or abandon the branch).

## Error handling

- If any command fails unexpectedly, stop immediately, show the error output, and wait for user instructions.
- Do not silently swallow errors or retry without reporting.
