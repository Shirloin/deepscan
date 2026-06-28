# Performance — Laravel

> Read `_base.md` first. This file extends the base checklist with
> Laravel-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Laravel codebase.

---

## Framework-Specific Context

**Framework:** Laravel
**Type:** Backend
**Language:** PHP
**Common patterns:** Eloquent, cache, queues, lazy collections

---

## Laravel-Specific Checklist

### Section 1 — Queries and Collections
- [ ] N+1 from missing eager loading (`with()`)
- [ ] Large datasets loaded into memory instead of lazy collections/chunking
- [ ] No database connection pooling strategy

**Expected behavior:** Eager loading, lazy collections for big datasets.

### Section 2 — Caching and Queues
- [ ] Laravel Cache facade not used for hot data
- [ ] Queue/job batching not used for bulk async work
- [ ] Route/config caching not enabled for production

**Expected behavior:** Cached hot data, batched jobs, prod route/config caching.

---

## Best Practices for This Stack

### Do
- Use `with()` eager loading to prevent N+1
- Use the Cache facade
- Batch queued jobs
- Use lazy collections for large datasets
- Enable route/config caching in production

### Don't
- Trigger N+1 in loops
- Load huge datasets fully into memory
- Skip production caching

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Relations | `with()` eager load | N+1 |
| Big dataset | `lazy()` / `chunk()` | `all()` into memory |
| Hot data | Cache facade | Re-query |
| Production | Route/config cache | Uncached |
