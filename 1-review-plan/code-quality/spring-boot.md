# Code Quality — Spring Boot

> Read `_base.md` first. This file extends the base checklist with
> Spring Boot-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Spring Boot codebase.

---

## Framework-Specific Context

**Framework:** Spring Boot
**Type:** Backend
**Language:** Java / Kotlin
**Common patterns:** Controller-service-repository, dependency injection, annotations

---

## Spring Boot-Specific Checklist

### Section 1 — Layer Violations
- [ ] Business logic in `@Controller` or `@RestController` classes
- [ ] Direct repository calls from controllers bypassing the service layer
- [ ] `@Repository` classes containing business logic
- [ ] Entities used as request/response bodies (exposing internal model)
- [ ] Missing DTO / request-response model separation

**Expected behavior:** Clean controller → service → repository layering with DTOs.

### Section 2 — Spring Patterns
- [ ] Field injection (`@Autowired` on fields) instead of constructor injection
- [ ] Not using `@Transactional` on service methods needing atomicity
- [ ] `@Transactional` on `private` methods (Spring AOP cannot intercept)
- [ ] Catching and swallowing exceptions inside `@Transactional` methods
- [ ] Not using Spring's exception hierarchy (`ResponseStatusException`, etc.)
- [ ] Missing `@Valid` on controller parameters for request validation

**Expected behavior:** Constructor injection, correct transaction boundaries.

### Section 3 — Configuration
- [ ] Hardcoded values instead of `@Value` or `@ConfigurationProperties`
- [ ] Application properties not typed with `@ConfigurationProperties`
- [ ] No profile-based configuration (`application-dev.properties`, etc.)
- [ ] Beans created with `new` instead of Spring-managed beans

**Expected behavior:** Typed, profile-aware configuration; Spring-managed beans.

---

## Best Practices for This Stack

### Do
- Use constructor injection
- Use DTOs for request/response
- Put `@Transactional` on the service layer
- Use `@ConfigurationProperties` for typed config
- Use `@Valid` for validation

### Don't
- Use field injection
- Put business logic in controllers
- Expose entities directly
- Catch exceptions silently in transactional methods

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Injection | Constructor | `@Autowired` field |
| Response | DTO | Entity returned directly |
| Transaction | On public service method | On `private` method |
| Config | `@ConfigurationProperties` | Hardcoded literals |
