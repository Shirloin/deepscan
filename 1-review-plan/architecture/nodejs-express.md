# Architecture — Node.js / Express

> Read `_base.md` first. This file extends the base checklist with
> Express-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing an Express codebase.

---

## Framework-Specific Context

**Framework:** Node.js / Express
**Type:** Backend
**Language:** JavaScript / TypeScript
**Common patterns:** Layered architecture, middleware stack

---

## Express-Specific Checklist

### Section 1 — Architectural Style
- [ ] No clear MVC or layered architecture
- [ ] Routers not modularized by domain
- [ ] Service and repository layers not separated

**Expected behavior:** Routes → controllers → services → repositories.

### Section 2 — Middleware and Errors
- [ ] Middleware stack not organized or ordered intentionally
- [ ] Error-handling middleware not placed last
- [ ] Cross-cutting concerns scattered rather than centralized

**Expected behavior:** Ordered middleware stack with a final error handler.

### Section 3 — Dependencies and Scaling
- [ ] No dependency injection approach (hard-wired requires)
- [ ] Project structure that does not scale with feature growth
- [ ] Tight coupling between layers

**Expected behavior:** DI/inversion of control; scalable structure.

---

## Best Practices for This Stack

### Do
- Use a layered architecture
- Modularize routers by domain
- Separate service and repository layers
- Centralize error handling

### Don't
- Put logic directly in routes
- Scatter cross-cutting concerns
- Hard-couple layers

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Layers | Route→service→repo | Logic in routes |
| Routers | By domain | One huge router |
| Errors | Central middleware | Per-route handling |
| Coupling | DI | Hard-wired requires |
