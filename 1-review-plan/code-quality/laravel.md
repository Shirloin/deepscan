# Code Quality — Laravel

> Read `_base.md` first. This file extends the base checklist with
> Laravel-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Laravel codebase.

---

## Framework-Specific Context

**Framework:** Laravel
**Type:** Backend
**Language:** PHP
**Common patterns:** MVC, Eloquent ORM, service container, Blade templates

---

## Laravel-Specific Checklist

### Section 1 — MVC Violations
- [ ] Business logic in controllers (fat controllers)
- [ ] Database queries directly in controllers without repositories or services
- [ ] Blade templates containing business logic or direct DB calls
- [ ] Models containing complex business rules (fat models)
- [ ] Missing service layer for complex operations

**Expected behavior:** Thin controllers, logic in services/actions.

### Section 2 — Eloquent Patterns
- [ ] N+1 queries from missing eager loading (`with()`)
- [ ] Raw queries with user input instead of the query builder
- [ ] Not using model scopes for reusable query logic
- [ ] Accessing model attributes without casts defined
- [ ] Missing `$fillable` or `$guarded` (mass assignment vulnerability)

**Expected behavior:** Eager-loaded, scoped, guarded models.

### Section 3 — Laravel-Specific
- [ ] Not using Form Requests for validation (validating in controllers)
- [ ] Rolling custom auth instead of built-in auth helpers
- [ ] Jobs not used for long-running tasks (done synchronously in requests)
- [ ] Not using Policies for authorization (manual role checks everywhere)
- [ ] Config read with `env()` directly in code (should use `config()`)
- [ ] Missing database transactions for multi-step operations

**Expected behavior:** Form Requests, Policies, Jobs, `config()` usage.

---

## Best Practices for This Stack

### Do
- Use Form Requests for validation
- Use Policies for authorization
- Use Jobs for async work
- Use service classes for business logic
- Eager load to prevent N+1

### Don't
- Write fat controllers or fat models
- Use `env()` in code (use `config()`)
- Skip `$fillable` on models
- Run raw queries with user input

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Validation | Form Request | Inline in controller |
| Authorization | Policy | Manual role checks |
| Query | `with()` eager load | N+1 lazy loads |
| Config | `config('x')` | `env('x')` in code |
