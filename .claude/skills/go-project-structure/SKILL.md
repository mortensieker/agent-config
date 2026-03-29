---
name: go-project-structure
description: Go project layout and module conventions. Use when setting up or navigating a Go project.
user-invocable: false
---

## LANGUAGE & TOOLCHAIN

- Go (latest stable version)
- Go modules (`go.mod` / `go.sum`) for dependency management
- `gofmt` and `golangci-lint` compliant code

## DIRECTORY LAYOUT

Follow the standard Go project layout:

```
cmd/<appname>/main.go   — application entry point (wiring only, no business logic)
internal/               — private packages, not importable from outside the module
pkg/                    — public packages (only if spec requires an importable library)
api/                    — OpenAPI / protobuf definitions (if applicable)
config/                 — configuration structs and loaders
migrations/             — database migration files (if applicable)
tests/                  — integration/e2e tests that span multiple packages
```

Rules:
- Prefer `internal/` over `pkg/` by default
- Never put business logic in `main.go` — only dependency wiring and server start
- Each package has a single, well-scoped responsibility

## CONFIGURATION

- Read config from environment variables by default
- Use `github.com/kelseyhightower/envconfig` or `github.com/spf13/viper` for structured config
- Never hardcode credentials, ports, or environment-specific values
- Config structs live in `config/`

## LOGGING

- Use `log/slog` (standard library structured logging, Go 1.21+)
- Log at appropriate levels: `Debug`, `Info`, `Warn`, `Error`
- Never log sensitive data (passwords, tokens, PII)

## IMPLEMENTATION STYLE

- Small, focused functions — one function does one thing
- Clear, unabbreviated naming; well-known short vars are acceptable: `i`, `err`, `ctx`, `r`, `w`
- Explicit over implicit
- Use constructor functions (`NewFoo(...)`) to enforce dependency injection
- All exported types and functions MUST have a godoc comment

## INTERFACES

- Define interfaces at the point of consumption, not declaration
- Keep interfaces small — prefer single-method interfaces where possible
- Accept interfaces, return concrete types (unless the spec demands otherwise)
- Use interfaces to enable testability

## BUILD CHECKLIST

- `go build ./...` succeeds with no errors
- `go mod tidy` — `go.mod` and `go.sum` are tidy
- `gofmt -l .` reports no files
