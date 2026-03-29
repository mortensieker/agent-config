---
name: go-error-handling
description: Go error handling patterns. Use when writing Go code that returns, wraps, or handles errors.
user-invocable: false
---

## CORE RULES

- Always return errors explicitly — never panic in library or business logic code
- Never ignore returned errors; use `_` only when genuinely intentional and comment why
- Panics are reserved for truly unrecoverable programmer errors (e.g. invalid init-time config)

## WRAPPING ERRORS

Add context at each layer so the error message forms a readable chain:

```go
if err != nil {
    return fmt.Errorf("getUserByID %d: %w", id, err)
}
```

- Use `%w` (not `%v`) so callers can unwrap with `errors.Is` / `errors.As`
- Add the operation or resource name — avoid generic messages like "failed"

## SENTINEL ERRORS

Define sentinel errors for expected, domain-level failure cases:

```go
var (
    ErrNotFound      = errors.New("not found")
    ErrUnauthorized  = errors.New("unauthorized")
    ErrAlreadyExists = errors.New("already exists")
)
```

- Declare them in the package that owns the concept
- Callers check with `errors.Is(err, ErrNotFound)`
- Do NOT expose implementation-level errors (e.g. `sql.ErrNoRows`) across package boundaries — wrap them into domain sentinels

## CUSTOM ERROR TYPES

Use a struct when callers need structured data from the error:

```go
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("validation: %s — %s", e.Field, e.Message)
}
```

- Implement `error` interface
- Callers extract with `errors.As(err, &ve)`

## ERROR PROPAGATION

- Return errors up the call stack — do not swallow them silently
- Log at the outermost point that has enough context; do NOT log and return
- Never return both a non-nil error and a non-zero value — callers must not depend on the value when an error is present

## CONCURRENCY & ERRORS

- Use `errgroup.Group` (`golang.org/x/sync/errgroup`) to collect errors from concurrent goroutines
- Always cancel the group context when an error occurs

## CHECKLIST

- No `_ = someFunc()` without an explanatory comment
- No bare `errors.New("error")` — messages must be specific
- No raw implementation errors crossing package boundaries
- Wrapped errors use `%w`
