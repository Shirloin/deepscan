# Code Quality — Vue

> Read `_base.md` first. This file extends the base checklist with
> Vue-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Vue codebase.

---

## Framework-Specific Context

**Framework:** Vue
**Type:** Frontend
**Language:** JavaScript / TypeScript
**Common patterns:** Component-based, reactivity system, Composition API, SFCs

---

## Vue-Specific Checklist

### Section 1 — Component Quality
- [ ] Options API used inconsistently with Composition API in the same project
- [ ] Logic in templates that belongs in computed properties or methods
- [ ] Missing `key` attribute in `v-for` directives
- [ ] `v-for` and `v-if` used on the same element (performance issue)
- [ ] Mutating props directly instead of emitting events
- [ ] Not using `defineProps` and `defineEmits` in `<script setup>`
- [ ] Large components not split into smaller composables

**Expected behavior:** Consistent API style, lean templates, event-based prop flow.

### Section 2 — Reactivity
- [ ] Directly adding new properties to reactive objects (Vue 2 — loses reactivity)
- [ ] Not using `ref()` vs `reactive()` correctly
- [ ] Watching deep objects without `deep: true` when needed
- [ ] Using `watch` where `computed` would be more appropriate
- [ ] Missing cleanup in `watchEffect` for side effects

**Expected behavior:** Correct reactivity primitives, computed for derived state.

### Section 3 — Composition API
- [ ] Logic not extracted into composables when reused across components
- [ ] Composables not following the `use` naming convention
- [ ] Mixing Options API and Composition API in the same component
- [ ] Not returning all reactive state from composables

**Expected behavior:** Reusable logic in well-named composables.

---

## Best Practices for This Stack

### Do
- Use Composition API with `<script setup>`
- Extract reusable logic into composables
- Use `computed` for derived state
- Emit events for child-to-parent communication

### Don't
- Mutate props
- Mix Options and Composition API in one component
- Use `v-for` and `v-if` together
- Skip `key` in lists

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Derived state | `computed` | `watch` writing back to a ref |
| Child→parent | `emit('update')` | Mutating a prop |
| List | `v-for` with `key` | `v-for` + `v-if` same element |
| Reuse | Composable `useThing()` | Duplicated logic |
