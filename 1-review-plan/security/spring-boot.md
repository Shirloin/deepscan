# Security — Spring Boot

> Read `_base.md` first. This file extends the base checklist with
> Spring Boot-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Spring Boot codebase.

---

## Framework-Specific Context

**Framework:** Spring Boot
**Type:** Backend
**Language:** Java / Kotlin
**Common patterns:** Spring Security, method security, JPA, Actuator

---

## Spring Boot-Specific Checklist

### Section 1 — Spring Security
- [ ] `permitAll()` used too broadly without understanding implications
- [ ] CSRF disabled without a documented reason and alternative protection
- [ ] CORS `allowedOrigins("*")` on authenticated endpoints
- [ ] Missing method-level security (`@PreAuthorize`, `@Secured`)
- [ ] Security config not using `SecurityFilterChain` bean (deprecated config)

**Expected behavior:** Least-privilege rules, method security, modern config.

### Section 2 — Input and Output
- [ ] `@RequestBody` without `@Valid` (no validation)
- [ ] Entities returned directly from controllers (exposes all fields)
- [ ] SQL via string concatenation with user data in `@Query`
- [ ] `@PathVariable`/`@RequestParam` used in queries without parameterization

**Expected behavior:** Validated input, DTO output, parameterized queries.

### Section 3 — Secrets and Config
- [ ] Credentials in `application.properties` committed to git
- [ ] Actuator endpoints exposed without security (`/actuator/env`, `/beans`)
- [ ] H2 console enabled in production
- [ ] Default Spring Security password logged at startup in production

**Expected behavior:** Externalized secrets, secured Actuator.

---

## Best Practices for This Stack

### Do
- Use `@Valid` on all `@RequestBody`
- Use DTOs for responses
- Use method-level security
- Secure or disable Actuator
- Keep secrets in Vault or environment

### Don't
- Expose entities directly
- Disable CSRF without an alternative
- Use `allowedOrigins("*")` on authenticated APIs
- Leave Actuator open

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Validation | `@Valid` | None |
| Query | Parameterized | String concatenation |
| Output | DTO | Entity returned raw |
| Actuator | Secured/disabled | `/actuator/env` open |
