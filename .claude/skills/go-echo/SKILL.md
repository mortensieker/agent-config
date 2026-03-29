---
name: go-echo
description: Go Echo framework REST API standards. Use when building a REST API with the Echo framework in Go.
user-invocable: false
---

Apply these standards when building a REST API with the [Echo](https://echo.labstack.com/) framework in Go.

First, apply all core Go skills:
- `go-project-structure` — directory layout, modules, interfaces, logging, config
- `go-error-handling` — error wrapping, sentinel errors
- `go-concurrency` — goroutines, channels, context propagation
- `go-testing` — table-driven tests, mocking, TDD workflow

Then apply the Echo-specific additions below.

---

## FRAMEWORK & DEPENDENCIES

- `github.com/labstack/echo/v4` — core framework
- `github.com/labstack/echo/v4/middleware` — standard middleware
- `github.com/go-playground/validator/v10` — request validation

## PROJECT LAYOUT (Echo additions)

```
cmd/<appname>/main.go       — wire Echo instance, register routes, start server
internal/
  handler/                  — Echo handler functions (one file per resource)
  middleware/               — custom Echo middleware
  service/                  — business logic (no Echo types here)
  repository/               — data access layer
  model/                    — domain types and request/response DTOs
config/                     — config structs and loader
```

Never import Echo types into `service/` or `repository/` — keep business logic framework-agnostic.

## ROUTING CONVENTIONS

- Register all routes in a dedicated `routes.go` or via each handler's `Register(e *echo.Echo)` method
- Group related routes: `e.Group("/api/v1/users")`
- Use named path params: `/users/:id` — accessed via `c.Param("id")`
- Version the API from day one: `/api/v1/...`

## REQUEST HANDLING

- Bind and validate in one step: `c.Bind(&req)` then `c.Validate(&req)`
- Always check bind/validate errors — return `400 Bad Request` with a structured error body
- Never trust client-supplied IDs — validate type, range, and existence server-side

## RESPONSE CONVENTIONS

All responses use JSON with a consistent envelope:

```json
// success
{ "data": { ... } }

// error
{ "error": { "code": "NOT_FOUND", "message": "resource not found" } }
```

Use `c.JSON(statusCode, payload)` — never write raw response bodies.

## ERROR HANDLING (Echo-specific)

- Implement a custom `echo.HTTPErrorHandler` to convert domain errors to HTTP responses
- Map sentinel errors (`ErrNotFound`, `ErrUnauthorized`, etc.) to HTTP status codes
- Never return stack traces or internal error messages to clients
- Log the full error server-side before returning a sanitised response

## MIDDLEWARE STACK

Apply in this order:

1. `middleware.RequestID()` — attach request ID to every request
2. `middleware.Logger()` — structured access logging
3. `middleware.Recover()` — catch panics, return 500
4. `middleware.CORS()` — configure allowed origins from config, never hardcoded
5. Auth middleware — validate JWT / session before protected route groups

## AUTHENTICATION (if applicable)

- Use `middleware.JWTWithConfig` or a custom middleware — never inline auth logic in handlers
- Store the parsed principal in `c.Set("user", principal)` and retrieve it in handlers
- Return `401 Unauthorized` for missing/invalid tokens, `403 Forbidden` for insufficient permissions

## TESTING (Echo-specific)

- Use `httptest.NewRecorder()` + Echo test helpers to test handlers in isolation
- Test the full request/response cycle: bind → validate → service call → response
- Mock the service layer via interfaces — never hit real databases in handler tests
- Integration tests (real DB) go in `tests/integration/`

## VALIDATION CHECKLIST (Echo additions)

In addition to the Go skill checklists, verify:
- All routes return correct HTTP status codes for happy and error paths
- Malformed request payloads return `400` with a structured error body
- Auth middleware blocks unauthenticated access to protected routes
- CORS origins are config-driven, not hardcoded
