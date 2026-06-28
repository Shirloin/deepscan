# Architecture — Vue

> Read `_base.md` first. This file extends the base checklist with
> Vue-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Vue codebase.

---

## Framework-Specific Context

**Framework:** Vue
**Type:** Frontend
**Language:** JavaScript / TypeScript
**Common patterns:** Pinia stores, composables, Nuxt (optional)

---

## Vue-Specific Checklist

### Section 1 — Store and State
- [ ] Pinia store not organized into modules by domain
- [ ] Store used for state that should be local to a component
- [ ] No clear separation between UI state and server state

**Expected behavior:** Domain-organized Pinia stores; minimal global state.

### Section 2 — Composables vs Components
- [ ] Logic that should be a composable kept in components
- [ ] Composables vs components decision unclear
- [ ] Options vs Composition API inconsistent across the project

**Expected behavior:** Reusable logic in composables; consistent API choice.

### Section 3 — Structure and Routing
- [ ] Feature folder structure missing for larger apps
- [ ] Router and middleware organization unclear
- [ ] Nuxt-specific conventions ignored when using Nuxt

**Expected behavior:** Feature folders, organized router/middleware.

---

## Best Practices for This Stack

### Do
- Organize Pinia stores by domain
- Extract reusable logic into composables
- Use a consistent API style
- Follow Nuxt conventions when applicable

### Don't
- Put local state in the global store
- Mix Options and Composition API arbitrarily
- Scatter routing logic

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Store | Domain modules | One mega store |
| Reuse | Composables | Copy-pasted logic |
| API style | Consistent | Mixed per file |
| Structure | Feature folders | Flat dump |
