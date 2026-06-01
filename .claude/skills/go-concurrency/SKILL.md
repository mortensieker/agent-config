---
name: go-concurrency
description: Go concurrency patterns. Use when writing goroutines, channels, or concurrent code in Go.
user-invocable: false
---

## CORE RULES

- Never start a goroutine you cannot stop or track
- Prefer channels over shared memory with mutexes
- Protect shared mutable state with `sync.Mutex` or `sync.RWMutex` when channels aren't appropriate

## CONTEXT PROPAGATION

Always pass `context.Context` as the first parameter to functions doing I/O or external calls:

```go
func (s *Service) GetUser(ctx context.Context, id int64) (*User, error) { ... }
```

- Call `defer cancel()` immediately after creating a cancellable context
- Check `ctx.Err()` in long-running loops
- Never store a context in a struct

## GOROUTINES

- Use `sync.WaitGroup` to wait for goroutines; use `errgroup.Group` for fan-out with error collection
- Pass data via parameters, not closures over loop variables (Go < 1.22)

## CHANNELS

- Unbuffered for synchronisation; buffered when the producer must not block
- Always close channels from the sender side
- Use `select` with `ctx.Done()` to make channel ops cancellable

## MUTEXES

- Lock for the shortest time necessary; use `defer mu.Unlock()`
- Use `sync.RWMutex` when reads significantly outnumber writes
- Never copy a mutex — always pass by pointer
