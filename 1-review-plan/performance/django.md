# Performance — Django

> Read `_base.md` first. This file extends the base checklist with
> Django-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Django codebase.

---

## Framework-Specific Context

**Framework:** Django
**Type:** Backend
**Language:** Python
**Common patterns:** ORM, cache framework, Celery, querysets

---

## Django-Specific Checklist

### Section 1 — ORM and Queries
- [ ] `select_related` / `prefetch_related` not used (N+1)
- [ ] Queryset lazy evaluation pitfalls (re-evaluated multiple times)
- [ ] Queries not analyzed with django-debug-toolbar

**Expected behavior:** N+1 eliminated; querysets evaluated once.

### Section 2 — Caching and Async
- [ ] Django cache framework (with Redis) not used for hot data
- [ ] Celery not used for async tasks
- [ ] No database connection pooling (e.g., pgBouncer)

**Expected behavior:** Cached hot data, async tasks via Celery, pooled connections.

---

## Best Practices for This Stack

### Do
- Use `select_related`/`prefetch_related`
- Use the Django cache framework with Redis
- Use Celery for async tasks
- Pool connections with pgBouncer

### Don't
- Trigger N+1 queries in loops/templates
- Re-evaluate the same queryset repeatedly
- Run heavy work synchronously

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Relations | `select_related` | N+1 |
| Queryset | Evaluated once | Re-evaluated in loop |
| Hot data | Cached | Re-queried |
| Heavy work | Celery | Inline in view |
