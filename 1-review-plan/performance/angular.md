# Performance — Angular

> Read `_base.md` first. This file extends the base checklist with
> Angular-specific performance concerns.
> Apply BOTH this file AND `_base.md` when reviewing an Angular codebase.

---

## Framework-Specific Context

**Framework:** Angular
**Type:** Frontend
**Language:** TypeScript
**Common patterns:** Change detection, RxJS, lazy loading

---

## Angular-Specific Checklist

### Section 1 — Change Detection
- [ ] `OnPush` change detection strategy not used on eligible components
- [ ] Missing `trackBy` in `*ngFor`
- [ ] Manual subscribe/unsubscribe where the `async` pipe is cleaner and safer

**Expected behavior:** OnPush + trackBy + async pipe.

### Section 2 — Pipes, Loading, and RxJS
- [ ] Impure pipes used where pure pipes suffice
- [ ] Modules not lazy loaded
- [ ] Inefficient RxJS (unnecessary `switchMap`, missing `shareReplay`)
- [ ] Bundle not optimized (esbuild/build budgets)

**Expected behavior:** Pure pipes, lazy loading, efficient streams, optimized bundle.

---

## Best Practices for This Stack

### Do
- Use OnPush change detection
- Add `trackBy` to `*ngFor`
- Use the async pipe
- Lazy load modules
- Use `shareReplay` for shared streams

### Don't
- Use impure pipes unnecessarily
- Subscribe manually without need
- Ship one large eager bundle

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Change detection | OnPush | Default everywhere |
| List | `trackBy` | None |
| Shared stream | `shareReplay` | Re-execute per subscriber |
| Loading | Lazy modules | Eager |
