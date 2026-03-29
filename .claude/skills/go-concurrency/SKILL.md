---
name: go-concurrency
description: Go concurrency patterns. Use when writing goroutines, channels, or concurrent code in Go.
user-invocable: false
---

## CORE RULES

- Never start a goroutine you cannot stop or track
- Prefer communicating via channels over sharing memory with mutexes
- Protect shared mutable state with `sync.Mutex` or `sync.RWMutex` when channels are not appropriate

## CONTEXT PROPAGATION

Always pass `context.Context` as the first parameter to functions that do I/O, call external services, or may be cancelled:

```go
func (s *Service) GetUser(ctx context.Context, id int64) (*User, error) { ... }
```

- Call `defer cancel()` immediately after creating a cancellable context
- Check `ctx.Err()` in long-running loops
- Never store a context in a struct — pass it explicitly

## GOROUTINES

```go
// Always provide a way to wait for goroutines to finish
var wg sync.WaitGroup
wg.Add(1)
go func() {
    defer wg.Done()
    // work
}()
wg.Wait()
```

- For fan-out with error collection, use `errgroup.Group` from `golang.org/x/sync/errgroup`
- Pass data into goroutines via parameters, not closures over loop variables (Go < 1.22)

## CHANNELS

- Unbuffered channels for synchronisation; buffered channels when the producer must not block
- Always close channels from the sender side, never the receiver
- Use `select` with a `ctx.Done()` case to make channel operations cancellable

```go
select {
case result := <-ch:
    // handle
case <-ctx.Done():
    return ctx.Err()
}
```

## MUTEXES

```go
type SafeCounter struct {
    mu sync.Mutex
    v  map[string]int
}

func (c *SafeCounter) Inc(key string) {
    c.mu.Lock()
    defer c.mu.Unlock()
    c.v[key]++
}
```

- Lock for the shortest time necessary
- Use `sync.RWMutex` when reads significantly outnumber writes
- Never copy a mutex — pass by pointer

## ONCE & POOLS

- `sync.Once` for one-time initialisation (e.g. singleton setup)
- `sync.Pool` for short-lived objects that are frequently allocated (e.g. buffers)

## CHECKLIST

- No goroutine leaks — every goroutine has a defined exit condition
- Contexts propagated through the full call chain
- `defer cancel()` present after every `context.WithCancel` / `context.WithTimeout`
- Race detector passes: `go test -race ./...`
