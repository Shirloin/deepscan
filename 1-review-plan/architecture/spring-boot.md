# Architecture — Spring Boot

> Read `_base.md` first. This file extends the base checklist with
> Spring Boot-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Spring Boot codebase.

---

## Framework-Specific Context

**Framework:** Spring Boot
**Type:** Backend
**Language:** Java / Kotlin
**Common patterns:** Controller-service-repository, Spring Data JPA, profiles

---

## Spring Boot-Specific Checklist

### Section 1 — Package Organization
- [ ] Package-by-layer used where package-by-feature would scale better
- [ ] No consistent organization strategy
- [ ] Cross-feature coupling through shared packages

**Expected behavior:** Consistent, preferably package-by-feature organization.

### Section 2 — Layering and Data Access
- [ ] Controller-service-repository pattern not followed
- [ ] Spring Data JPA repositories doing business logic
- [ ] Repository design not aligned with aggregates/domain

**Expected behavior:** Clean layering; repositories for data access only.

### Section 3 — Events, Config, and Scaling
- [ ] `ApplicationEvent`-driven patterns used ad hoc
- [ ] Spring Cloud microservices boundaries unclear when applicable
- [ ] Configuration not managed with profiles

**Expected behavior:** Deliberate events, profile-based config, clear boundaries.

---

## Best Practices for This Stack

### Do
- Prefer package-by-feature
- Follow controller-service-repository
- Manage config with profiles
- Use events deliberately

### Don't
- Put business logic in repositories
- Couple features through shared packages
- Hardcode environment config

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Packages | By feature | By layer only |
| Layers | Controller→service→repo | Logic in repo |
| Config | Profiles | Hardcoded |
| Events | Deliberate | Ad hoc everywhere |
