# Code Quality — Node.js / Express

> Read `_base.md` first. This file extends the base checklist with
> Express-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing an Express codebase.

---

## Framework-Specific Context

**Framework:** Node.js / Express
**Type:** Backend
**Language:** JavaScript / TypeScript
**Common patterns:** Middleware pipeline, route handlers, MVC/layered

---

## Express-Specific Checklist

### Section 1 — Route Quality
- [ ] Business logic written directly in route handlers
- [ ] No middleware for cross-cutting concerns (auth, validation, logging)
- [ ] Routes not grouped by resource or feature
- [ ] Missing input validation on routes
- [ ] Error handling not delegated to centralized error middleware
- [ ] Mixing `router.get()` and `app.get()` inconsistently

**Expected behavior:** Thin routes delegating to controllers/services.

### Section 2 — Middleware
- [ ] Middleware doing too many things (validation AND auth AND logging)
- [ ] Order-sensitive middleware not clearly documented
- [ ] Missing error-handling middleware as the last `app.use()`
- [ ] Not calling `next()` or `next(err)` correctly
- [ ] Synchronous operations in middleware that should be async

**Expected behavior:** Single-responsibility middleware with a final error handler.

### Section 3 — Async Patterns
- [ ] Mixing `async/await` with `.then().catch()` chains in the same codebase
- [ ] Missing `try/catch` around `await` in route handlers
- [ ] Unhandled promise rejections in middleware
- [ ] Async route handlers not wrapped to catch errors automatically

**Expected behavior:** Consistent async style with errors funneled to middleware.

---

## Best Practices for This Stack

### Do
- Separate routes from controllers
- Use `express-async-errors` or an async wrapper
- Centralize error handling
- Validate with Joi/Zod at the route level

### Don't
- Put business logic in routes
- Mix async patterns
- Forget to call `next(err)` on errors
- Skip input validation

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Route logic | Delegates to service | Inline business logic |
| Errors | Central error middleware | `try/catch` returning in each route |
| Async handler | Wrapped to catch rejections | Unhandled rejection |
| Validation | Joi/Zod middleware | Manual ad-hoc checks |
