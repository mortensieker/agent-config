---
name: go-error-handling
description: Go error handling patterns. Use when writing Go code that returns, wraps, or handles errors.
user-invocable: false
---

## CORE RULES

- Always return errors explicitly — never panic in library or business logic code
- Never ignore returned errors; use `_` only when intentional and comment why
- Log at the outermost point that has context — do NOT log and return

## WRAPPING

Add context at each layer using `%w` so callers can unwrap:

```go
return fmt.Errorf("getUserByID %d: %w", id, err)
```

## SENTINEL ERRORS

```go
var (
    ErrNotFound      = errors.New("not found")
    ErrUnauthorized  = errors.New("unauthorized")
    ErrAlreadyExists = errors.New("already exists")
)
```

- Declare in the package that owns the concept
- Never expose implementation-level errors (e.g. `sql.ErrNoRows`) across package boundaries — wrap into domain sentinels
- Callers check with `errors.Is` / `errors.As`

## CUSTOM ERROR TYPES

Use a struct when callers need structured data:

```go
type ValidationError struct {
    Field   string
    Message string
}

func (e *ValidationError) Error() string {
    return fmt.Sprintf("validation: %s — %s", e.Field, e.Message)
}
```

## CONCURRENCY

Use `errgroup.Group` (`golang.org/x/sync/errgroup`) to collect errors from concurrent goroutines.
