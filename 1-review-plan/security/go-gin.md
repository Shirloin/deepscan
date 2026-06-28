# Security — Go / Gin

> Read `_base.md` first. This file extends the base checklist with
> Gin-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Go/Gin codebase.

---

## Framework-Specific Context

**Framework:** Go / Gin
**Type:** Backend
**Language:** Go
**Common patterns:** Middleware, JWT auth, struct binding, explicit errors

---

## Gin-Specific Checklist

### Section 1 — Input Handling
- [ ] User input bound with `c.ShouldBind` but not validated afterward
- [ ] SQL queries built with `fmt.Sprintf` using user input (injection)
- [ ] File paths constructed from user input without sanitization
- [ ] Missing request size limits (DoS via large payloads)

**Expected behavior:** Validated binding, parameterized queries, size limits.

### Section 2 — Auth Implementation
- [ ] JWT middleware not applied to all protected route groups
- [ ] JWT secret hardcoded as a string constant
- [ ] Missing token expiry validation (`exp` claim)
- [ ] No rate limiting on auth endpoints

**Expected behavior:** Consistent JWT middleware, env secret, expiry checks.

### Section 3 — Go-Specific Security
- [ ] Goroutine launched without timeout context (can run indefinitely)
- [ ] `panic` not recovered in Gin middleware (crashes the server)
- [ ] Error messages returned to client including internal details
- [ ] TLS configuration not set (running HTTP in production)

**Expected behavior:** Bounded goroutines, recovery middleware, sanitized errors.

---

## Best Practices for This Stack

### Do
- Validate all bound structs
- Use parameterized queries
- Apply JWT middleware on route groups
- Set request size limits
- Use context with timeout for goroutines

### Don't
- Use `fmt.Sprintf` for SQL
- Hardcode the JWT secret
- Return internal errors to clients
- Skip recovery middleware

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| SQL | `db.Query("... $1", id)` | `fmt.Sprintf` query |
| JWT secret | Env variable | Hardcoded constant |
| Errors | Generic message | Internal detail leaked |
| Panic | Recovery middleware | Unrecovered crash |
