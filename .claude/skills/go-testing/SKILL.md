---
name: go-testing
description: Go testing patterns and tools. Use when writing tests for Go code.
user-invocable: false
---

## TOOLS

- `go test ./...` — standard runner
- `github.com/stretchr/testify/assert` + `testify/require` — assertions
- `github.com/stretchr/testify/mock` — interface mocks
- `net/http/httptest` — HTTP handler/server testing

## FILE CONVENTIONS

- `foo_test.go` lives next to `foo.go`
- Same package (`package foo`) for white-box tests; `package foo_test` for black-box
- Integration tests: `_integration_test.go` suffix or `tests/integration/`

## TABLE-DRIVEN TESTS

Default pattern for functions with multiple input/output variations — use `t.Run` with a `cases` slice of `{name, input, want, wantErr}`.

## TDD WORKFLOW

1. Write the test first — verify it fails before implementing
2. Implement the minimum code to make it pass
3. Refactor while keeping tests green

## MOCKING

- Define interfaces in the consuming package; use `testify/mock` against them
- Use `httptest` for HTTP layer tests
- Do NOT mock the database in unit tests — use in-memory DB or testcontainers for integration tests

## TEST SCOPE

| Layer | Tool | What to test |
|---|---|---|
| Pure logic | `testing` + testify | Input/output, edge cases |
| HTTP handlers | `httptest` + testify | Status codes, response bodies |
| Repositories | Real DB / testcontainers | SQL queries, migrations |

## QUALITY BAR

- Tests are first-class code — review them like production code
- Test behaviour, not implementation; `go test -race ./...` must pass
