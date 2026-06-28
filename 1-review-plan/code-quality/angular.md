# Code Quality — Angular

> Read `_base.md` first. This file extends the base checklist with
> Angular-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing an Angular codebase.

---

## Framework-Specific Context

**Framework:** Angular
**Type:** Frontend
**Language:** TypeScript
**Common patterns:** Module-based, dependency injection, RxJS observables, decorators

---

## Angular-Specific Checklist

### Section 1 — Component and Module Quality
- [ ] Components doing too much — missing service extraction
- [ ] Logic in templates instead of the component class or pipes
- [ ] No `OnPush` change detection on pure/presentational components
- [ ] Missing `trackBy` in `*ngFor` directives
- [ ] Subscribing to Observables in templates manually instead of the `async` pipe
- [ ] Not unsubscribing from Observables in `ngOnDestroy` (memory leak)
- [ ] God modules — one module for the entire application
- [ ] Lazy-loaded modules not implemented for feature areas

**Expected behavior:** Lean components delegating to services, OnPush + async pipe,
feature modules with lazy loading.

### Section 2 — Dependency Injection
- [ ] Services provided in root when they should be feature-scoped
- [ ] Circular dependency between services
- [ ] Direct instantiation of services instead of DI
- [ ] Missing `providedIn` declaration on injectable services

**Expected behavior:** Services injected through DI with correct scope.

### Section 3 — RxJS Usage
- [ ] Nested subscriptions instead of `switchMap`, `mergeMap`, `concatMap`
- [ ] Not using `takeUntil` or `takeUntilDestroyed` for cleanup
- [ ] Using `subscribe` for side effects that should use `tap`
- [ ] Creating Subjects where a simpler Observable would work
- [ ] Not handling error and complete cases in subscriptions

**Expected behavior:** Flattening operators for composition, declarative cleanup,
proper error handling.

---

## Best Practices for This Stack

### Do
- Use OnPush change detection
- Use the async pipe for Observables in templates
- Follow the smart/dumb component pattern
- Split features into lazy-loaded modules
- Compose streams with RxJS operators

### Don't
- Subscribe in a component and forget to unsubscribe
- Put logic in templates
- Use the `any` type
- Skip lazy loading for large features

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Subscription | `async` pipe in template | Manual `subscribe` without cleanup |
| Change detection | `OnPush` on presentational | Default everywhere |
| List rendering | `*ngFor` with `trackBy` | `*ngFor` without `trackBy` |
| Nested async | `switchMap` | Subscribe inside subscribe |
| Cleanup | `takeUntilDestroyed()` | No unsubscribe |
