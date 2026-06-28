# Code Quality — Django

> Read `_base.md` first. This file extends the base checklist with
> Django-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Django codebase.

---

## Framework-Specific Context

**Framework:** Django
**Type:** Backend
**Language:** Python
**Common patterns:** MTV, ORM, class-based views, apps

---

## Django-Specific Checklist

### Section 1 — View and URL Patterns
- [ ] Function-based views for complex logic that should be class-based
- [ ] Business logic directly in views instead of services or model methods
- [ ] Fat views — doing data access, transformation, AND rendering
- [ ] URL patterns not namespaced for large projects
- [ ] Missing `login_required` or permission decorators on protected views

**Expected behavior:** Thin views, namespaced URLs, protected endpoints.

### Section 2 — Model Quality
- [ ] Fat models — all business logic on the model with no service layer
- [ ] No `__str__` method on models
- [ ] Missing `Meta` class with ordering and verbose names
- [ ] Direct string queries with `.raw()` using user input (SQL injection risk)
- [ ] No custom managers for commonly reused querysets
- [ ] Missing `select_related` / `prefetch_related` causing N+1

**Expected behavior:** Well-defined models with managers and no N+1.

### Section 3 — Django-Specific Patterns
- [ ] Not using Django forms or serializers for input validation
- [ ] Settings not split by environment (`settings/base.py`, `settings/prod.py`)
- [ ] `DEBUG=True` not controlled by environment variable
- [ ] `SECRET_KEY` hardcoded in the settings file
- [ ] Static files not configured for production (whitenoise or S3)

**Expected behavior:** Validated input, environment-split settings.

---

## Best Practices for This Stack

### Do
- Use class-based views for CRUD
- Use a service layer for complex business logic
- Use custom managers and `select_related`/`prefetch_related`
- Split settings by environment

### Don't
- Write fat views or fat models with all logic
- Hardcode `SECRET_KEY`
- Skip form validation
- Use `.raw()` with user input

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Query | `select_related` | N+1 loops |
| Reusable query | Custom manager | Repeated filter chains |
| Settings | Split per environment | Single `settings.py` |
| Secret | From environment | Hardcoded `SECRET_KEY` |
