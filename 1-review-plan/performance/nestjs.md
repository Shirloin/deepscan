# Performance — NestJS

> Read `_base.md` first. This file extends the base checklist with
> NestJS-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a NestJS codebase.

---

## Framework-Specific Context

**Framework:** NestJS
**Type:** Backend
**Language:** TypeScript
**Common patterns:** Caching, interceptors, queues, streaming

---

## NestJS-Specific Checklist

### Section 1 — Caching
- [ ] `@nestjs/cache-manager` not used where caching would help
- [ ] No interceptor-based response caching for cacheable endpoints
- [ ] Cache invalidation strategy missing

**Expected behavior:** Cache hot endpoints with clear invalidation.

### Section 2 — Data Access and Heavy Work
- [ ] TypeORM queries not optimized (N+1, missing relations strategy)
- [ ] Bull queue not used for heavy/background tasks
- [ ] Large payloads not streamed
- [ ] No compression interceptor

**Expected behavior:** Optimized queries, queued heavy work, streamed payloads.

---

## Best Practices for This Stack

### Do
- Use `@nestjs/cache-manager`
- Use interceptor-based response caching
- Optimize TypeORM queries
- Use Bull for heavy tasks

### Don't
- Re-query hot data uncached
- Run heavy work in the request path
- Buffer large payloads

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Hot endpoint | Cached | Recomputed |
| Heavy task | Bull queue | Inline in request |
| Query | Optimized joins | N+1 |
| Large payload | Streamed | Buffered |
