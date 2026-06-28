# Code Quality — FastAPI

> Read `_base.md` first. This file extends the base checklist with
> FastAPI-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a FastAPI codebase.

---

## Framework-Specific Context

**Framework:** FastAPI
**Type:** Backend
**Language:** Python
**Common patterns:** Async routes, Pydantic models, dependency injection

---

## FastAPI-Specific Checklist

### Section 1 — Route and Endpoint Quality
- [ ] Business logic directly in route functions instead of services
- [ ] Route functions too long — should delegate to a service layer
- [ ] Missing `response_model` on endpoints (no output validation/filtering)
- [ ] Not using dependency injection for shared logic (auth, DB sessions, config)
- [ ] Missing `status_code` on POST/DELETE endpoints

**Expected behavior:** Thin endpoints with `response_model` and DI.

### Section 2 — Pydantic Usage
- [ ] Not using Pydantic models for request bodies (using `dict` instead)
- [ ] Missing field validators on Pydantic models
- [ ] Not using `response_model` to filter sensitive fields
- [ ] Pydantic models used as database models (mixing ORM and schema layer)
- [ ] Missing `Config` class settings (e.g., `orm_mode`)

**Expected behavior:** Pydantic for all I/O, separate from ORM models.

### Section 3 — Dependency Injection
- [ ] DB sessions created inside route functions instead of injected via `Depends`
- [ ] Auth logic repeated in routes instead of a shared dependency
- [ ] Not using `Depends` for services — instantiating directly
- [ ] Missing lifespan management for startup/shutdown events

**Expected behavior:** Shared concerns provided through `Depends`.

### Section 4 — Async Patterns
- [ ] Blocking I/O (synchronous DB calls) in async route handlers
- [ ] Not using `async def` for I/O-bound operations
- [ ] Mixing sync and async without `run_in_executor`
- [ ] Missing `await` on async database calls

**Expected behavior:** Non-blocking async throughout the request path.

---

## Best Practices for This Stack

### Do
- Use Pydantic for all I/O
- Set `response_model` on all endpoints
- Use `Depends` for DI
- Use `async` for all I/O-bound operations
- Keep business logic in a service layer

### Don't
- Put logic in routes
- Skip `response_model`
- Use `dict` instead of Pydantic
- Block the event loop with sync I/O

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Request body | Pydantic model | Raw `dict` |
| Response | `response_model` filters fields | Returns ORM object directly |
| DB session | Injected via `Depends` | Created inside route |
| I/O | `await` async client | Sync call in `async def` |
