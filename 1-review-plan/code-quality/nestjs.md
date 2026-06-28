# Code Quality — NestJS

> Read `_base.md` first. This file extends the base checklist with
> NestJS-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a NestJS codebase.

---

## Framework-Specific Context

**Framework:** NestJS
**Type:** Backend
**Language:** TypeScript
**Common patterns:** Modules, decorators, dependency injection, guards/pipes/interceptors

---

## NestJS-Specific Checklist

### Section 1 — Module Structure
- [ ] Everything in one module instead of feature modules
- [ ] No clear domain separation between modules
- [ ] Circular module dependencies
- [ ] Services imported across module boundaries without proper exports
- [ ] Not using shared modules for common services

**Expected behavior:** Cohesive feature modules with explicit exports.

### Section 2 — Decorators and DI
- [ ] Controllers doing business logic instead of delegating to services
- [ ] Services doing direct database access instead of using repositories
- [ ] Missing `@Injectable()` decorator on services
- [ ] Not using guards for authorization (auth implemented in services)
- [ ] Not using pipes for validation (validation in controllers)
- [ ] Missing `@UseGuards()`, `@UsePipes()`, `@UseInterceptors()` where appropriate

**Expected behavior:** Controllers delegate; cross-cutting concerns use the right primitive.

### Section 3 — Code Patterns
- [ ] DTOs not defined for request/response bodies
- [ ] No `class-validator` decorators on DTOs
- [ ] Missing `class-transformer` for response serialization
- [ ] Exception filters not used — throwing raw errors from services
- [ ] Not using built-in exception types (`NotFoundException`, `BadRequestException`)

**Expected behavior:** Typed DTOs, validated input, framework exceptions.

---

## Best Practices for This Stack

### Do
- Use feature modules
- Use DTOs with class-validator
- Use guards for auth, pipes for validation, interceptors for cross-cutting concerns
- Use exception filters

### Don't
- Put logic in controllers
- Skip DTOs
- Use plain objects instead of class instances
- Bypass the DI system

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Controller | Delegates to service | Contains business logic |
| Validation | DTO + `ValidationPipe` | Manual checks in controller |
| Auth | Guard | Inline checks in service |
| Errors | `NotFoundException` etc. | `throw new Error()` |
