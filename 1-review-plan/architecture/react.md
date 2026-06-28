# Architecture — React

> Read `_base.md` first. This file extends the base checklist with
> React-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a React codebase.

---

## Framework-Specific Context

**Framework:** React
**Type:** Frontend
**Language:** JavaScript / TypeScript
**Common patterns:** Component-based, unidirectional data flow, hooks

---

## React-Specific Checklist

### Section 1 — Folder Structure
- [ ] Type-based folders (`components/`, `hooks/`) that don't scale vs feature-based
- [ ] No clear ownership boundary between features
- [ ] Shared code not separated from feature-specific code

**Expected behavior:** Feature-based structure for non-trivial apps.

### Section 2 — Component Hierarchy and State Placement
- [ ] State lifted higher than necessary, causing wide re-renders
- [ ] Global store used for state that is local to one component
- [ ] Context used for high-frequency updates (should be local or a store)

**Expected behavior:** State lives at the lowest common owner; local → context → store.

### Section 3 — Server State and Data Layer
- [ ] Raw `useEffect` + `fetch` for server state instead of React Query/SWR
- [ ] No separation between the data-fetching layer and the UI layer
- [ ] Custom hooks not used to encapsulate data access

**Expected behavior:** Dedicated server-state library; data layer separate from UI.

---

## Best Practices for This Stack

### Do
- Organize by feature
- Use React Query/SWR for server state
- Keep state at the lowest sensible level
- Encapsulate data access in custom hooks

### Don't
- Put everything in a global store
- Use context for high-frequency state
- Mix fetching and rendering concerns

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Structure | Feature folders | Flat `components/` dump |
| Server state | React Query/SWR | `useEffect` + `fetch` |
| State scope | Lowest owner | Everything global |
| Data layer | Custom hooks | Fetch inline in JSX |
