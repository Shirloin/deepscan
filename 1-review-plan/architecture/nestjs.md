# Architecture — NestJS

> Read `_base.md` first. This file extends the base checklist with
> NestJS-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a NestJS codebase.

---

## Framework-Specific Context

**Framework:** NestJS
**Type:** Backend
**Language:** TypeScript
**Common patterns:** Modules, providers, CQRS, events

---

## NestJS-Specific Checklist

### Section 1 — Module System
- [ ] Feature encapsulation weak; services leaked across modules
- [ ] No shared/core module strategy
- [ ] Circular module dependencies

**Expected behavior:** Encapsulated feature modules with explicit exports.

### Section 2 — Provider Scope and Patterns
- [ ] Provider scope (singleton/request/transient) chosen without reason
- [ ] CQRS with `@nestjs/cqrs` applied inconsistently or unnecessarily
- [ ] Event system with EventEmitter2 used ad hoc

**Expected behavior:** Deliberate provider scope; consistent CQRS/event use.

### Section 3 — Cross-Cutting and Microservices
- [ ] Interceptors vs middleware vs guards used without a clear rule
- [ ] Microservices boundaries unclear when applicable
- [ ] Transport/messaging concerns leaking into domain code

**Expected behavior:** Clear primitive-selection rule; clean service boundaries.

---

## Best Practices for This Stack

### Do
- Encapsulate feature modules
- Choose provider scope deliberately
- Apply a clear interceptor/middleware/guard rule
- Keep microservice boundaries clean

### Don't
- Leak services across modules
- Overuse CQRS for trivial flows
- Mix transport concerns into domain logic

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Modules | Encapsulated features | Leaky exports |
| Scope | Deliberate | Default everywhere |
| Cross-cutting | Right primitive | Logic in random place |
| CQRS | Where it adds value | Everywhere |
