# Code Quality — React

> Read `_base.md` first. This file extends the base checklist with
> React-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a React codebase.

---

## Framework-Specific Context

**Framework:** React
**Type:** Frontend
**Language:** JavaScript / TypeScript
**Common patterns:** Component-based, declarative UI, hooks, unidirectional data flow

---

## React-Specific Checklist

### Section 1 — Component Quality
- [ ] Components over 200 lines that mix concerns (data fetching + rendering + state)
- [ ] Class components used where functional components + hooks would be simpler
- [ ] Direct state mutation instead of `setState` or the `useState` setter
- [ ] Missing `key` prop in list renders, or using the array index as the key
- [ ] Overuse of `useRef` for values that should be state
- [ ] Prop types not defined (PropTypes or TypeScript interface missing)
- [ ] Default props not defined for optional props

**Expected behavior:** Small, focused functional components with typed props,
correct stable keys, and immutable state updates.

### Section 2 — Hooks Quality
- [ ] Custom hooks not following the `use` naming convention
- [ ] Logic that belongs in a custom hook kept inline in a component
- [ ] `useState` for derived values that should be computed from existing state
- [ ] Overuse of `useContext` causing unnecessary re-renders
- [ ] `useReducer` not used when state transitions are complex
- [ ] Repeated stateful logic not extracted into a custom hook

**Expected behavior:** Reusable stateful logic lives in well-named custom hooks;
derived values are computed, not stored.

### Section 3 — Patterns
- [ ] Inheritance used instead of composition
- [ ] Render props or HOC used where hooks would be cleaner
- [ ] Missing error boundary around data-dependent component trees
- [ ] Conditional rendering using `&&` with a non-boolean left side (renders `0`)
- [ ] Spreading all props onto DOM elements (`<div {...props}>`) without filtering

**Expected behavior:** Composition over inheritance, hooks over HOC/render props,
error boundaries around async trees, explicit boolean conditions.

---

## Best Practices for This Stack

### Do
- Use functional components with hooks for all new code
- Extract reusable logic into custom hooks
- Define TypeScript interfaces for props
- Wrap data-dependent trees in error boundaries
- Memoize only where measured to help

### Don't
- Mutate state directly
- Use the array index as a list key
- Mix data fetching and rendering in one component
- Use class components for new code

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| List key | Stable unique id | Array index |
| State update | `setCount(c => c + 1)` | `state.count++` |
| Derived value | Compute in render / `useMemo` | Store in `useState` + sync effect |
| Conditional render | `cond ? <X/> : null` | `count && <X/>` (renders `0`) |
| Reusable logic | Custom `useThing()` hook | Copy-pasted in each component |
