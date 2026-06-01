---
name: go-echo
description: Go Echo framework REST API standards. Use when building a REST API with the Echo framework in Go.
user-invocable: false
---

Apply all core Go skills first (`go-project-structure`, `go-error-handling`, `go-concurrency`, `go-testing`), then add the Echo-specific conventions below.

## DEPENDENCIES

- `github.com/labstack/echo/v4` + `middleware`
- `github.com/go-playground/validator/v10` — request validation

## PROJECT LAYOUT

```
cmd/<appname>/main.go   — wire Echo, register routes, start server
internal/
  handler/              — Echo handler functions (one file per resource)
  middleware/           — custom Echo middleware
  service/              — business logic (no Echo types here)
  repository/           — data access
  model/                — domain types and request/response DTOs
```

Never import Echo types into `service/` or `repository/`.

## ROUTING

- Group related routes: `e.Group("/api/v1/users")`
- Version the API from day one: `/api/v1/...`
- Named path params: `/users/:id` — accessed via `c.Param("id")`

## REQUEST HANDLING

- Bind and validate in one step: `c.Bind(&req)` then `c.Validate(&req)`
- Return `400 Bad Request` with a structured error body on bind/validate failure
- Never trust client-supplied IDs — validate server-side

## RESPONSE CONVENTIONS

```json
{ "data": { ... } }
{ "error": { "code": "NOT_FOUND", "message": "resource not found" } }
```

Use `c.JSON(statusCode, payload)` — never write raw response bodies.

## ERROR HANDLING

- Implement a custom `echo.HTTPErrorHandler` to map domain errors to HTTP status codes
- Never return stack traces or internal messages to clients
- Log the full error server-side before returning a sanitised response

## MIDDLEWARE ORDER

1. `middleware.RequestID()`
2. `middleware.Logger()`
3. `middleware.Recover()`
4. `middleware.CORS()` — origins from config, never hardcoded
5. Auth middleware before protected route groups
