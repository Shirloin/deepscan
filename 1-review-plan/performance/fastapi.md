# Performance — FastAPI

> Read `_base.md` first. This file extends the base checklist with
> FastAPI-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a FastAPI codebase.

---

## Framework-Specific Context

**Framework:** FastAPI
**Type:** Backend
**Language:** Python
**Common patterns:** Async I/O, background tasks, async DB drivers

---

## FastAPI-Specific Checklist

### Section 1 — Async Correctness
- [ ] Async vs sync endpoint decision wrong (blocking call in `async def`)
- [ ] SQLAlchemy async session not used with async drivers
- [ ] Connection pooling with asyncpg not configured

**Expected behavior:** Truly async I/O end to end with pooled connections.

### Section 2 — Background Work and Caching
- [ ] Background tasks vs Celery decision unclear
- [ ] Large payloads not streamed
- [ ] No Redis caching (e.g., aioredis) for hot data

**Expected behavior:** Right background-work tool; streamed payloads; cached hot data.

---

## Best Practices for This Stack

### Do
- Choose async vs sync endpoints deliberately
- Use async SQLAlchemy sessions
- Stream large responses
- Cache hot data with Redis

### Don't
- Block the event loop with sync I/O in `async def`
- Run heavy work inline when Celery fits
- Buffer large payloads

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Endpoint | Async with async I/O | `async def` + blocking call |
| DB | Async session + pool | Sync driver |
| Heavy work | Background/Celery | Inline |
| Hot data | Cached | Re-queried |
