# Architecture — Laravel

> Read `_base.md` first. This file extends the base checklist with
> Laravel-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Laravel codebase.

---

## Framework-Specific Context

**Framework:** Laravel
**Type:** Backend
**Language:** PHP
**Common patterns:** Service-repository, actions, events/listeners, jobs

---

## Laravel-Specific Checklist

### Section 1 — Service and Action Design
- [ ] No service-repository pattern for complex domains
- [ ] Single-responsibility operations not extracted into action classes
- [ ] Business logic spread across controllers and models

**Expected behavior:** Services/actions hold business logic.

### Section 2 — Events, Jobs, and Queues
- [ ] Event/listener architecture used ad hoc or not at all
- [ ] Jobs and queues not organized by concern
- [ ] Synchronous handling of work that should be queued

**Expected behavior:** Deliberate events; organized job/queue structure.

### Section 3 — API and Providers
- [ ] API resources/collections not used for response shaping
- [ ] Service providers not organized; bindings scattered
- [ ] No clear registration strategy for bindings

**Expected behavior:** API resources for output; organized providers.

---

## Best Practices for This Stack

### Do
- Use the service-repository pattern
- Extract action classes for single operations
- Use API resources for responses
- Organize service providers

### Don't
- Spread logic across controllers and models
- Handle heavy work synchronously
- Scatter container bindings

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Logic | Services/actions | In controllers/models |
| Output | API resources | Raw model arrays |
| Work | Queued jobs | Synchronous in request |
| Bindings | Organized providers | Scattered |
