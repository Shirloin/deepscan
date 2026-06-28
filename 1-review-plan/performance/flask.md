# Performance — Flask

> Read `_base.md` first. This file extends the base checklist with
> Flask-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Flask codebase.

---

## Framework-Specific Context

**Framework:** Flask
**Type:** Backend
**Language:** Python
**Common patterns:** Gunicorn workers, caching, SQLAlchemy sessions, Celery

---

## Flask-Specific Checklist

### Section 1 — Serving and Sessions
- [ ] Gunicorn worker count/type not configured for the workload
- [ ] SQLAlchemy session management leaking connections
- [ ] No connection pooling configured

**Expected behavior:** Right worker config; well-managed pooled sessions.

### Section 2 — Caching and I/O
- [ ] Flask-Caching (with Redis) not used for hot data
- [ ] Celery not used for async tasks
- [ ] Large files not streamed in responses
- [ ] Blocking I/O in request handlers

**Expected behavior:** Cached hot data, async via Celery, streamed large files.

---

## Best Practices for This Stack

### Do
- Configure gunicorn workers for the workload
- Use Flask-Caching with Redis
- Manage SQLAlchemy sessions and pool connections
- Use Celery for async tasks
- Stream large file responses

### Don't
- Leak DB connections/sessions
- Run blocking I/O in request handlers
- Buffer large files in memory

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Workers | Tuned gunicorn | Defaults |
| Sessions | Scoped + pooled | Leaked connections |
| Hot data | Flask-Caching | Re-query |
| Large file | Streamed | Buffered |
