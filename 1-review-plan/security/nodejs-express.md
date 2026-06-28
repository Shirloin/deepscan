# Security — Node.js / Express

> Read `_base.md` first. This file extends the base checklist with
> Express-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing an Express codebase.

---

## Framework-Specific Context

**Framework:** Node.js / Express
**Type:** Backend
**Language:** JavaScript / TypeScript
**Common patterns:** Middleware pipeline, JWT/session auth, REST APIs

---

## Express-Specific Checklist

### Section 1 — Middleware Security
- [ ] No `helmet` middleware for security headers
- [ ] CORS configured with `*` on authenticated routes
- [ ] No rate limiting on auth or sensitive endpoints
- [ ] Body parser size limit not configured (DoS via large payloads)
- [ ] Missing CSRF protection for cookie-based session auth

**Expected behavior:** Helmet + scoped CORS + rate limiting + body limits.

### Section 2 — Auth Implementation
- [ ] JWT verified without checking the `alg` field (algorithm confusion)
- [ ] Session secret hardcoded in code
- [ ] Sessions not invalidated on logout (stateless JWT without blocklist)
- [ ] Password comparison not using `bcrypt.compare` (timing attack)

**Expected behavior:** Pinned JWT alg, env secrets, constant-time comparison.

### Section 3 — Express-Specific
- [ ] `res.json()` returning stack traces in production
- [ ] `X-Powered-By` header not disabled (exposes Express version)
- [ ] No input sanitization middleware (`express-validator` or equivalent)
- [ ] File uploads without type/size validation (`multer` limits missing)

**Expected behavior:** No leaked internals, validated input and uploads.

---

## Best Practices for This Stack

### Do
- Use `helmet` for headers
- Use `express-rate-limit`
- Use `bcrypt` for passwords
- Use `express-validator` for input
- Disable `X-Powered-By`

### Don't
- Use wildcard CORS on auth routes
- Hardcode session secrets
- Return stack traces to clients
- Skip body size limits

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Headers | `helmet()` | None |
| CORS | Explicit origin | `origin: '*'` on auth |
| Password | `bcrypt.compare` | `===` comparison |
| Errors | Sanitized message | Stack trace in response |
