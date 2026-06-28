# Performance — React

> Read `_base.md` first. This file extends the base checklist with
> React-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a React codebase.

---

## Framework-Specific Context

**Framework:** React
**Type:** Frontend
**Language:** JavaScript / TypeScript
**Common patterns:** Reconciliation, memoization, code splitting

---

## React-Specific Checklist

### Section 1 — Rendering and Memoization
- [ ] `React.memo` applied without measurement (can hurt as much as help)
- [ ] `useCallback`/`useMemo` with wrong/missing deps or premature use
- [ ] Context not split, causing wide re-renders on unrelated updates
- [ ] Profiler not used to confirm actual bottlenecks

**Expected behavior:** Memoize based on Profiler measurement; split contexts.

### Section 2 — Loading and Lists
- [ ] No code splitting via `Suspense` + `lazy` for heavy routes/components
- [ ] Long lists rendered without virtualization
- [ ] Images not optimized (no sizing, lazy loading, modern formats)

**Expected behavior:** Lazy-load heavy code; virtualize long lists; optimize images.

---

## Best Practices for This Stack

### Do
- Measure with the Profiler before optimizing
- Split context to limit re-render scope
- Virtualize long lists
- Code-split with `lazy` + `Suspense`

### Don't
- Wrap everything in `memo`/`useMemo` blindly
- Render thousands of rows without virtualization
- Ship unoptimized images

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Memoization | Measured, targeted | Everywhere by default |
| Long list | Virtualized | Full render |
| Heavy component | `lazy` + `Suspense` | Eager import |
| Context | Split by concern | One global context |
