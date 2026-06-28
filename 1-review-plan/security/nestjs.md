# Security — NestJS

> Read `_base.md` first. This file extends the base checklist with
> NestJS-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a NestJS codebase.

---

## Framework-Specific Context

**Framework:** NestJS
**Type:** Backend
**Language:** TypeScript
**Common patterns:** Guards, pipes, interceptors, DTOs, modules

---

## NestJS-Specific Checklist

### Section 1 — Guard and Auth
- [ ] No global auth guard — endpoints are public by default
- [ ] `@Public()` decorator used without understanding the implications
- [ ] Missing role-based guards on admin endpoints
- [ ] JWT strategy not validating the `exp` claim

**Expected behavior:** Secure-by-default global guard with explicit `@Public()`.

### Section 2 — Validation and Serialization
- [ ] Missing `ValidationPipe` globally or per-controller
- [ ] DTOs without `class-validator` decorators (no input validation)
- [ ] `excludeExtraneousValues` not set on `ClassSerializerInterceptor`
- [ ] Exposing internal entity fields without `@Exclude()`

**Expected behavior:** Validated input, allowlisted serialized output.

### Section 3 — NestJS-Specific
- [ ] Modules importing sensitive services without proper encapsulation
- [ ] Config module not using `@nestjs/config` (hardcoded values)
- [ ] No throttling with `@nestjs/throttler` on public endpoints
- [ ] Exception filters not sanitizing error messages before sending

**Expected behavior:** Encapsulated modules, config module, throttling.

---

## Best Practices for This Stack

### Do
- Use a global `ValidationPipe`
- Use `ClassSerializerInterceptor` with `excludeExtraneousValues`
- Use a global auth guard with `@Public()` for open endpoints
- Use `ThrottlerModule`

### Don't
- Skip the validation pipe
- Expose entity classes in responses
- Return raw exceptions to clients
- Skip role guards on admin routes

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Auth default | Global guard + `@Public()` | Public by default |
| Validation | `ValidationPipe` + DTO | None |
| Serialization | `@Exclude()` sensitive fields | Entity returned raw |
| Rate limit | `ThrottlerModule` | None |
