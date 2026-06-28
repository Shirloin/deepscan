# Architecture — Django

> Read `_base.md` first. This file extends the base checklist with
> Django-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Django codebase.

---

## Framework-Specific Context

**Framework:** Django
**Type:** Backend
**Language:** Python
**Common patterns:** MTV, apps, DRF, signals, managers

---

## Django-Specific Checklist

### Section 1 — App Organization
- [ ] Monolithic app instead of splitting into focused apps
- [ ] App boundaries unclear; circular imports between apps
- [ ] No guidance on when to create a new app

**Expected behavior:** Cohesive apps split along clear domain boundaries.

### Section 2 — MTV and Service Layer
- [ ] No place for a service layer in the MTV pattern
- [ ] Business logic scattered between views and models
- [ ] DRF viewset structure inconsistent

**Expected behavior:** Clear home for business logic; consistent DRF structure.

### Section 3 — Signals and Data Access
- [ ] Signals used where a direct call would be clearer
- [ ] Custom managers/querysets not used for reusable data access
- [ ] Data access logic duplicated across views

**Expected behavior:** Signals only when decoupling is needed; managers for reuse.

---

## Best Practices for This Stack

### Do
- Split into focused apps
- Add a service layer for complex logic
- Use custom managers/querysets
- Keep DRF viewsets consistent

### Don't
- Build one giant app
- Overuse signals
- Duplicate query logic across views

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Apps | Focused per domain | One mega app |
| Logic | Service layer | Split across view/model |
| Data access | Custom managers | Duplicated querysets |
| Signals | Where decoupling helps | For direct flows |
