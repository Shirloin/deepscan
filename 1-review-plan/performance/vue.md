# Performance — Vue

> Read `_base.md` first. This file extends the base checklist with
> Vue-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Vue codebase.

---

## Framework-Specific Context

**Framework:** Vue
**Type:** Frontend
**Language:** JavaScript / TypeScript
**Common patterns:** Reactivity caching, computed, async components

---

## Vue-Specific Checklist

### Section 1 — Rendering Optimizations
- [ ] `v-once` not used for truly static content
- [ ] `v-memo` not used for expensive list optimization
- [ ] Computed properties not leveraged for caching derived values
- [ ] Pinia getter caching not utilized

**Expected behavior:** Cache derived values; skip re-render of static content.

### Section 2 — Loading and Lists
- [ ] Async component loading not used for heavy components
- [ ] `KeepAlive` not used for expensive, frequently toggled components
- [ ] Large lists rendered without virtual scrolling

**Expected behavior:** Lazy/async heavy components, virtual scrolling for big lists.

---

## Best Practices for This Stack

### Do
- Use `v-once` for static content
- Use `v-memo` for list optimization
- Use computed for derived state caching
- Use `KeepAlive` for expensive components

### Don't
- Recompute derived values on every render
- Render large lists without virtualization
- Eagerly load heavy components

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Derived value | `computed` | Method called in template |
| Static block | `v-once` | Re-rendered each update |
| Big list | Virtual scroll | Full render |
| Heavy component | Async + `KeepAlive` | Eager, re-created |
