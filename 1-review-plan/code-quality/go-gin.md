# Code Quality — Go / Gin

> Read `_base.md` first. This file extends the base checklist with
> Gin-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Go/Gin codebase.

---

## Framework-Specific Context

**Framework:** Go / Gin
**Type:** Backend
**Language:** Go
**Common patterns:** Handler-service-repository, interfaces, middleware, explicit errors

---

## Gin-Specific Checklist

### Section 1 — Handler Quality
- [ ] Business logic directly in Gin handlers
- [ ] Handlers too long — should delegate to a service layer
- [ ] Not using binding tags for request validation (`binding:"required"`)
- [ ] Missing error responses — returning 200 for all responses
- [ ] Not using proper HTTP status codes (`c.JSON(200, ...)` for errors)

**Expected behavior:** Thin handlers, validated binding, correct status codes.

### Section 2 — Go-Specific Patterns
- [ ] Not handling errors explicitly (ignoring returned error values)
- [ ] Using `panic` for recoverable errors instead of returning errors
- [ ] Not using interfaces for service dependencies (tight coupling)
- [ ] Global variables for shared state instead of dependency injection
- [ ] Goroutines launched without WaitGroup or context cancellation
- [ ] Context not propagated through the call chain

**Expected behavior:** Explicit error handling, interfaces, context propagation.

### Section 3 — Code Organization
- [ ] All handlers in one file with no domain separation
- [ ] No clear separation between handler, service, and repository layers
- [ ] Mixing HTTP concerns with business logic
- [ ] Not using middleware for cross-cutting concerns (auth, logging, rate limiting)

**Expected behavior:** Domain-separated packages with clear layers.

---

## Best Practices for This Stack

### Do
- Handle errors explicitly
- Use interfaces for dependencies
- Propagate `context.Context`
- Use middleware for cross-cutting concerns
- Separate packages by domain

### Don't
- Ignore errors
- Use `panic` for recoverable errors
- Use global mutable state
- Put business logic in handlers

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Error | `if err != nil { return err }` | `_ = doThing()` |
| Dependency | Interface injected | Concrete global |
| Validation | Binding tags | Manual parsing |
| Goroutine | With context/WaitGroup | Fire-and-forget |
