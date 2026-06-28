# Architecture — FastAPI

> Read `_base.md` first. This file extends the base checklist with
> FastAPI-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a FastAPI codebase.

---

## Framework-Specific Context

**Framework:** FastAPI
**Type:** Backend
**Language:** Python
**Common patterns:** Routers, dependency injection tree, async services

---

## FastAPI-Specific Checklist

### Section 1 — Router and DI Organization
- [ ] Routers not organized by domain
- [ ] Dependency injection tree unstructured or deeply nested
- [ ] Shared dependencies duplicated across routers

**Expected behavior:** Domain routers with a clean DI tree.

### Section 2 — Service and Schema Layers
- [ ] No async service layer; logic in route functions
- [ ] Pydantic schemas not separated (request/response/internal)
- [ ] Schema and ORM models conflated

**Expected behavior:** Async service layer; separated schema concerns.

### Section 3 — Data Access and Background Work
- [ ] No repository pattern over SQLAlchemy/Tortoise
- [ ] Background tasks vs Celery decision unclear
- [ ] Long-running work done inline in request path

**Expected behavior:** Repository pattern; deliberate background-work choice.

---

## Best Practices for This Stack

### Do
- Organize routers by domain
- Design a clean DI tree
- Separate request/response/internal schemas
- Use a repository pattern

### Don't
- Put logic in routes
- Conflate schema and ORM models
- Run heavy work inline

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Routers | By domain | One file |
| Schemas | Separated layers | One model for all |
| Logic | Async service layer | In route function |
| Heavy work | Background/Celery | Inline in request |
