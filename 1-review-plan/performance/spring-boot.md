# Performance — Spring Boot

> Read `_base.md` first. This file extends the base checklist with
> Spring Boot-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Spring Boot codebase.

---

## Framework-Specific Context

**Framework:** Spring Boot
**Type:** Backend
**Language:** Java / Kotlin
**Common patterns:** JPA, caching, connection pooling, async

---

## Spring Boot-Specific Checklist

### Section 1 — JPA and Database
- [ ] JPA N+1 not addressed with `@EntityGraph` or `JOIN FETCH`
- [ ] HikariCP connection pool not tuned for load
- [ ] Queries fetching more data than needed

**Expected behavior:** N+1 eliminated; pool sized for workload.

### Section 2 — Caching and Concurrency
- [ ] Spring Cache abstraction not used; no eviction policy
- [ ] No async with `@Async`/`CompletableFuture` for parallelizable work
- [ ] Virtual threads (Java 21) not considered for high-concurrency I/O
- [ ] GraalVM native compilation trade-offs not evaluated where relevant

**Expected behavior:** Caching with eviction; async/virtual threads where they help.

---

## Best Practices for This Stack

### Do
- Use `@EntityGraph`/`JOIN FETCH` to avoid N+1
- Use the Spring Cache abstraction with eviction
- Tune HikariCP
- Use `@Async`/virtual threads for I/O-bound work

### Don't
- Leave N+1 queries in place
- Cache without an eviction policy
- Use default pool sizing under heavy load

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Relations | `JOIN FETCH` | N+1 lazy loads |
| Cache | With eviction | None / never evicts |
| Pool | Tuned HikariCP | Defaults under load |
| Concurrency | `@Async`/virtual threads | Blocking serial calls |
