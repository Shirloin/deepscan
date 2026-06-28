# Security — Next.js

> Read `_base.md` first. This file extends the base checklist with
> Next.js-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Next.js codebase.

---

## Framework-Specific Context

**Framework:** Next.js
**Type:** Full-stack
**Language:** JavaScript / TypeScript
**Common patterns:** Server/client components, API routes, server actions, middleware

---

## Next.js-Specific Checklist

### Section 1 — Server-Side Security
- [ ] API routes with no input validation
- [ ] Missing authentication check in Server Actions
- [ ] `getServerSideProps` exposing sensitive data in props (visible in source)
- [ ] `NEXT_PUBLIC_` prefix on values that should be server-only
- [ ] No rate limiting on API routes

**Expected behavior:** Validated, authenticated, rate-limited server code.

### Section 2 — Client-Side Security
- [ ] Sensitive logic in client components that should be server-only
- [ ] API keys exposed via `NEXT_PUBLIC_` env vars
- [ ] User input rendered with `dangerouslySetInnerHTML`
- [ ] Missing Content Security Policy headers in `next.config.js`

**Expected behavior:** Secrets and sensitive logic stay server-side.

### Section 3 — Headers and Config
- [ ] No security headers configured in `next.config.js`
- [ ] Missing `X-Frame-Options`, `X-Content-Type-Options`, `Referrer-Policy`
- [ ] CORS not configured for API routes
- [ ] No `middleware.ts` for centralized auth enforcement

**Expected behavior:** Security headers + centralized middleware auth.

---

## Best Practices for This Stack

### Do
- Use server components for sensitive logic
- Use server-only env vars without `NEXT_PUBLIC_`
- Set security headers in `next.config.js`
- Use middleware for auth

### Don't
- Expose API keys with `NEXT_PUBLIC_`
- Skip validation on API routes
- Put sensitive logic in client components

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Secret env | `API_KEY` (server) | `NEXT_PUBLIC_API_KEY` |
| API route | Validates input | Trusts body |
| Auth | `middleware.ts` | Per-route ad hoc |
| Headers | Set in config | None |
