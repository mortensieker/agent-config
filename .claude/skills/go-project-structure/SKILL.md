---
name: go-project-structure
description: Go project layout and module conventions. Use when setting up or navigating a Go project.
user-invocable: false
---

## TOOLCHAIN

- Go (latest stable), Go modules (`go.mod` / `go.sum`)
- Code must pass `gofmt` and `golangci-lint`

## DIRECTORY LAYOUT

```
cmd/<appname>/main.go   — entry point: wiring only, no business logic
internal/               — private packages
pkg/                    — public packages (only if spec requires importable library)
config/                 — config structs and loaders
migrations/             — database migrations (if applicable)
tests/                  — integration/e2e tests spanning multiple packages
```

- Prefer `internal/` over `pkg/` by default
- Each package has a single, well-scoped responsibility

## CONFIGURATION

- Read from environment variables; use `envconfig` or `viper` for structured config
- Never hardcode credentials, ports, or environment-specific values

## LOGGING

- Use `log/slog` (Go 1.21+) at appropriate levels: `Debug`, `Info`, `Warn`, `Error`
- Never log sensitive data

## IMPLEMENTATION STYLE

- Small, focused functions; clear unabbreviated names
- Constructor functions (`NewFoo(...)`) for dependency injection
- All exported types and functions MUST have a godoc comment

## INTERFACES

- Define interfaces at the point of consumption, not declaration
- Keep interfaces small — single-method where possible
- Accept interfaces, return concrete types
