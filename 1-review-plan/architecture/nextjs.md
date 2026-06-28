# Architecture — Next.js

> Read `_base.md` first. This file extends the base checklist with
> Next.js-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Next.js codebase.

---

## Framework-Specific Context

**Framework:** Next.js
**Type:** Full-stack
**Language:** JavaScript / TypeScript
**Common patterns:** App/pages router, server/client components, route handlers

---

## Next.js-Specific Checklist

### Section 1 — Router and Component Boundaries
- [ ] App router and pages router mixed inconsistently
- [ ] No clear server vs client component decision tree
- [ ] Client components used where server components would suffice

**Expected behavior:** One router; server-first with a documented boundary.

### Section 2 — Backend Surface
- [ ] API routes, server actions, and route handlers used interchangeably without a rule
- [ ] Business logic duplicated across route handlers and server actions
- [ ] No shared service layer behind the backend surface

**Expected behavior:** A clear rule for which backend primitive to use.

### Section 3 — Data Fetching and Cross-Cutting
- [ ] Data fetching pattern inconsistent (server fetch vs React Query mix)
- [ ] Middleware not used for cross-cutting concerns
- [ ] Monorepo structure unclear when applicable

**Expected behavior:** Consistent data strategy; middleware for cross-cutting concerns.

---

## Best Practices for This Stack

### Do
- Commit to one router
- Define a server/client component decision tree
- Use middleware for cross-cutting concerns
- Share a service layer behind API routes and server actions

### Don't
- Mix routers inconsistently
- Duplicate logic across backend primitives
- Mix data-fetching strategies without reason

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Router | One consistent router | App + pages mixed |
| Component | Server default | Client everywhere |
| Backend | Documented primitive choice | Ad hoc mix |
| Data | One strategy | Server fetch + RQ randomly |
