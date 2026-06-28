# Performance — Node.js / Express

> Read `_base.md` first. This file extends the base checklist with
> Express-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing an Express codebase.

---

## Framework-Specific Context

**Framework:** Node.js / Express
**Type:** Backend
**Language:** JavaScript / TypeScript
**Common patterns:** Event loop, streams, clustering, caching

---

## Express-Specific Checklist

### Section 1 — Concurrency and I/O
- [ ] No clustering / multi-process to use multiple cores
- [ ] Large file/data responses not streamed (buffered in memory)
- [ ] Synchronous operations blocking the event loop in the request path

**Expected behavior:** Cluster across cores; stream large payloads; stay non-blocking.

### Section 2 — Caching and Connections
- [ ] No compression middleware
- [ ] No database connection pooling
- [ ] No caching layer (e.g., Redis) for hot reads

**Expected behavior:** Compression, pooled connections, Redis caching.

---

## Best Practices for This Stack

### Do
- Use clustering for multi-core usage
- Stream large responses
- Use compression middleware
- Pool DB connections and cache with Redis

### Don't
- Run synchronous work in the request path
- Buffer huge responses in memory
- Open a new DB connection per request

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Cores | Clustered | Single process |
| Large data | Streamed | Buffered |
| DB | Connection pool | New connection per request |
| Hot reads | Cached | Re-query every time |
