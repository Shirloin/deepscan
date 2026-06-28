# Security — FastAPI

> Read `_base.md` first. This file extends the base checklist with
> FastAPI-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a FastAPI codebase.

---

## Framework-Specific Context

**Framework:** FastAPI
**Type:** Backend
**Language:** Python
**Common patterns:** Pydantic validation, OAuth2/JWT, dependency injection

---

## FastAPI-Specific Checklist

### Section 1 — Input Validation
- [ ] Route parameters not validated with Pydantic (using raw `str`/`int`)
- [ ] Missing `Field()` validators (no min/max length, pattern)
- [ ] File uploads without content-type and size validation
- [ ] Query parameters used in database queries without validation

**Expected behavior:** Pydantic + `Field` validators on all input.

### Section 2 — Auth and JWT
- [ ] OAuth2 scheme implemented without token expiry validation
- [ ] JWT secret hardcoded instead of from environment
- [ ] No token refresh mechanism — using long-lived access tokens
- [ ] Missing auth dependency on protected routes
- [ ] CORS `allow_origins=["*"]` on authenticated endpoints

**Expected behavior:** Validated, short-lived tokens; auth on every protected route.

### Section 3 — FastAPI-Specific
- [ ] Swagger UI exposed in production with sensitive endpoint docs
- [ ] `debug=True` in `uvicorn.run()` in production
- [ ] Internal server error details returned to client (missing exception handlers)
- [ ] Dependencies not used for rate limiting on public endpoints

**Expected behavior:** Docs disabled in prod, sanitized errors, rate limiting.

---

## Best Practices for This Stack

### Do
- Use Pydantic with `Field` validators for all input
- Use `Depends` for auth on every protected route
- Keep the JWT secret in an environment variable
- Disable docs in production

### Don't
- Use wildcard CORS on auth routes
- Hardcode the JWT secret
- Expose Swagger in production
- Skip token expiry validation

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Input | Pydantic + `Field` | Raw `str` param |
| Auth | `Depends(get_current_user)` | No dependency |
| JWT secret | Env variable | Hardcoded constant |
| Errors | Exception handler | Raw traceback returned |
