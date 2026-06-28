# Architecture — Go / Gin

> Read `_base.md` first. This file extends the base checklist with
> Gin-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Go/Gin codebase.

---

## Framework-Specific Context

**Framework:** Go / Gin
**Type:** Backend
**Language:** Go
**Common patterns:** Clean architecture, interface-driven design, manual DI

---

## Gin-Specific Checklist

### Section 1 — Package Structure
- [ ] Packages not organized by domain
- [ ] Clean architecture layers (handler/service/repo) not respected
- [ ] Import cycles between packages

**Expected behavior:** Domain packages with clear layer separation.

### Section 2 — Interfaces and DI
- [ ] Services not defined behind interfaces (tight coupling)
- [ ] Dependency injection not done (globals/`init()` wiring)
- [ ] Concrete types depended on across layers

**Expected behavior:** Interface-driven design with manual DI at composition root.

### Section 3 — Middleware and Context
- [ ] Middleware stack not organized
- [ ] `context.Context` not propagated through layers
- [ ] Cross-cutting concerns duplicated

**Expected behavior:** Organized middleware; context propagated end to end.

---

## Best Practices for This Stack

### Do
- Organize packages by domain
- Drive services with interfaces
- Do DI at the composition root
- Propagate context through layers

### Don't
- Create import cycles
- Depend on concrete types across layers
- Use globals for wiring

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Packages | By domain | By technical type only |
| Dependencies | Interfaces | Concrete globals |
| DI | Composition root | `init()` wiring |
| Context | Propagated | Dropped between layers |
