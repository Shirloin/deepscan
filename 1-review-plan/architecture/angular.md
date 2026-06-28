# Architecture — Angular

> Read `_base.md` first. This file extends the base checklist with
> Angular-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing an Angular codebase.

---

## Framework-Specific Context

**Framework:** Angular
**Type:** Frontend
**Language:** TypeScript
**Common patterns:** Module architecture, DI, smart/dumb components

---

## Angular-Specific Checklist

### Section 1 — Module Architecture
- [ ] No core/shared/feature module separation
- [ ] Shared module importing feature-specific code
- [ ] Module boundaries unclear; everything in one module

**Expected behavior:** Core (singletons), shared (reusable), feature modules.

### Section 2 — Component and Service Design
- [ ] Smart vs dumb component pattern not followed
- [ ] Business logic in components instead of services
- [ ] Service layer not organized by domain

**Expected behavior:** Smart containers + dumb presentational; logic in services.

### Section 3 — State, Lazy Loading, and Boundaries
- [ ] NgRx/Akita store structure inconsistent or overused for trivial state
- [ ] Lazy loading not used for feature areas
- [ ] Barrel files creating circular dependencies
- [ ] Standalone components vs modules used inconsistently

**Expected behavior:** Consistent store, lazy-loaded features, clean boundaries.

---

## Best Practices for This Stack

### Do
- Separate core/shared/feature modules
- Follow the smart/dumb component pattern
- Lazy load feature areas
- Keep a consistent standalone-vs-module strategy

### Don't
- Put all code in one module
- Overuse a global store for trivial state
- Create circular barrel imports

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Modules | core/shared/feature | One god module |
| Components | Smart + dumb | Logic in every component |
| Loading | Lazy feature modules | Everything eager |
| Store | Used for shared state | Used for trivial local state |
