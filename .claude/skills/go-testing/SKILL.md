---
name: go-testing
description: Go testing patterns and tools. Use when writing tests for Go code.
user-invocable: false
---

## TEST RUNNER & TOOLS

- `go test ./...` — standard test runner, no external runner needed
- `github.com/stretchr/testify/assert` and `testify/require` — cleaner assertions
- `github.com/stretchr/testify/mock` — interface mocks
- `net/http/httptest` — HTTP handler and server testing

## FILE CONVENTIONS

- Test file: `foo_test.go` lives in the same directory as `foo.go`
- Use the same package name for white-box tests (`package foo`)
- Use `package foo_test` for black-box tests of the public API
- Integration tests: suffix file with `_integration_test.go` or place in `tests/integration/`

## TABLE-DRIVEN TESTS

Default pattern for any function with multiple input/output variations:

```go
func TestFoo(t *testing.T) {
    cases := []struct {
        name    string
        input   string
        want    string
        wantErr bool
    }{
        {"happy path", "input", "expected", false},
        {"empty input", "", "", true},
    }
    for _, tc := range cases {
        t.Run(tc.name, func(t *testing.T) {
            got, err := Foo(tc.input)
            if tc.wantErr {
                require.Error(t, err)
                return
            }
            require.NoError(t, err)
            assert.Equal(t, tc.want, got)
        })
    }
}
```

## TDD WORKFLOW

1. Write the test first — it must fail before any implementation
2. Write the minimum code to make it pass
3. Refactor while keeping tests green

A test that cannot fail is worthless — verify it fails before implementing.

## MOCKING

- Define the interface in the consuming package
- Use `testify/mock` to generate mocks for interfaces
- Prefer `httptest.NewRecorder()` and `httptest.NewServer()` for HTTP layer tests
- Do NOT mock the database in unit tests — use an in-memory DB or test containers for integration tests

## TEST SCOPE

| Layer | Tool | What to test |
|---|---|---|
| Pure logic | `testing` + testify | Input/output, edge cases |
| HTTP handlers | `httptest` + testify | Status codes, response bodies |
| Repositories | Real DB / testcontainers | SQL queries, migrations |
| Integration | `tests/integration/` | Cross-package flows |

## QUALITY BAR

- Tests are first-class code — review them like production code
- No tests that only assert `err == nil`
- Coverage matters, but correctness matters more — test behaviour, not implementation

## VALIDATION CHECKLIST

- `go test ./...` passes
- `go test -race ./...` passes (no data races)
- Integration tests gated behind a build tag or env var if they require external services
