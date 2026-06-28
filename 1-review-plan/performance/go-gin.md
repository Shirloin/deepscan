# Performance — Go / Gin

> Read `_base.md` first. This file extends the base checklist with
> Gin-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Go/Gin codebase.

---

## Framework-Specific Context

**Framework:** Go / Gin
**Type:** Backend
**Language:** Go
**Common patterns:** Goroutines, sync.Pool, connection pooling, profiling

---

## Gin-Specific Checklist

### Section 1 — Concurrency and Allocation
- [ ] No goroutine pool / bounded concurrency for parallel work
- [ ] `sync.Pool` not used to reuse objects in hot paths
- [ ] Avoidable heap allocations in hot paths
- [ ] Context timeout not propagated for downstream calls

**Expected behavior:** Bounded concurrency, reused objects, minimal allocations.

### Section 2 — I/O and Profiling
- [ ] No gzip middleware
- [ ] Database connection pool not tuned
- [ ] pprof not used to find bottlenecks

**Expected behavior:** Compression, tuned pool, profiled hot paths.

---

## Best Practices for This Stack

### Do
- Use goroutine pools / bounded concurrency
- Use `sync.Pool` for object reuse
- Propagate context timeouts
- Use gzip middleware and tune the DB pool
- Profile with pprof

### Don't
- Spawn unbounded goroutines
- Allocate needlessly in hot paths
- Leave the DB pool at defaults under load

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Concurrency | Bounded pool | Unbounded goroutines |
| Hot path | `sync.Pool` reuse | Allocate per request |
| Downstream | Context timeout | No deadline |
| Bottleneck | pprof-driven | Guessing |
