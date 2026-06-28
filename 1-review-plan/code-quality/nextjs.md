# Code Quality — Next.js

> Read `_base.md` first. This file extends the base checklist with
> Next.js-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Next.js codebase.

---

## Framework-Specific Context

**Framework:** Next.js
**Type:** Full-stack
**Language:** JavaScript / TypeScript
**Common patterns:** File-based routing, server/client components, SSR/SSG/ISR

---

## Next.js-Specific Checklist

### Section 1 — Pages and Routing
- [ ] `pages/` directory patterns mixed with `app/` directory (inconsistency)
- [ ] Fetching data in client components that should be server components
- [ ] Using `useEffect` + `fetch` where a server component would work
- [ ] Missing `loading.tsx` and `error.tsx` for route segments
- [ ] Not using dynamic imports for heavy client components
- [ ] Hardcoded route strings instead of a routes constants file

**Expected behavior:** One consistent router, server-first data fetching,
loading/error boundaries per segment.

### Section 2 — Data Fetching
- [ ] `getServerSideProps` used when `getStaticProps` + ISR would suffice
- [ ] No `revalidate` on static pages that need periodic updates
- [ ] Client-side fetching without SWR or React Query (no cache/revalidation)
- [ ] Same data fetched in multiple components instead of at the layout level
- [ ] No error handling in `getServerSideProps` / `getStaticProps`

**Expected behavior:** Correct rendering strategy per page, deduplicated fetches.

### Section 3 — App Router Specific (app/ directory)
- [ ] `use client` on components that don't need client interactivity
- [ ] Passing non-serializable props from server to client components
- [ ] Not using React `cache()` to dedupe server-side requests
- [ ] Missing Suspense boundaries around async server components

**Expected behavior:** Server components by default, client only where needed.

### Section 4 — API Routes
- [ ] No input validation on API route handlers
- [ ] Missing authentication middleware on protected API routes
- [ ] Returning sensitive data in API responses
- [ ] No rate limiting on public API routes

**Expected behavior:** Validated, authenticated, rate-limited route handlers.

---

## Best Practices for This Stack

### Do
- Use server components by default; client only when needed
- Use ISR for mostly-static content
- Provide `error.tsx` and `loading.tsx` for every route segment
- Validate input on all API routes

### Don't
- Fetch in `useEffect` when a server component would work
- Use `getServerSideProps` when static would do
- Skip input validation on API routes

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Component type | Server component default | `use client` everywhere |
| Static content | `getStaticProps` + `revalidate` | `getServerSideProps` |
| Client fetch | SWR / React Query | Raw `useEffect` + `fetch` |
| Routes | Constants file | Hardcoded strings |
