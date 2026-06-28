# Performance — Next.js

> Read `_base.md` first. This file extends the base checklist with
> Next.js-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Next.js codebase.

---

## Framework-Specific Context

**Framework:** Next.js
**Type:** Full-stack
**Language:** JavaScript / TypeScript
**Common patterns:** Static/dynamic rendering, ISR, streaming, edge runtime

---

## Next.js-Specific Checklist

### Section 1 — Rendering Strategy
- [ ] Dynamic rendering used where static would be faster
- [ ] ISR not configured for stale-while-revalidate content
- [ ] Server vs client component split not optimized for performance
- [ ] No streaming with Suspense for slow data

**Expected behavior:** Static/ISR where possible; stream slow data.

### Section 2 — Assets and Bundle
- [ ] `next/image` Image component not used
- [ ] Font optimization not used
- [ ] Bundle analyzer not run; large client bundles
- [ ] Edge runtime not considered for latency-sensitive routes

**Expected behavior:** Optimized images/fonts, lean bundles, edge where it helps.

---

## Best Practices for This Stack

### Do
- Prefer static/ISR rendering
- Use the Image and font optimizations
- Stream slow content with Suspense
- Analyze the bundle

### Don't
- Force dynamic rendering unnecessarily
- Ship unoptimized images/fonts
- Push large logic into client components

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Rendering | Static/ISR | Always dynamic |
| Images | `next/image` | Raw `<img>` |
| Slow data | Suspense streaming | Block whole page |
| Bundle | Analyzed & lean | Unmeasured & large |
