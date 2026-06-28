# Security — Angular

> Read `_base.md` first. This file extends the base checklist with
> Angular-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing an Angular codebase.

---

## Framework-Specific Context

**Framework:** Angular
**Type:** Frontend
**Language:** TypeScript
**Common patterns:** Built-in sanitization, HttpClient, route guards, interceptors

---

## Angular-Specific Checklist

### Section 1 — Angular Security Model
- [ ] Bypassing built-in sanitization with `bypassSecurityTrust*` methods
- [ ] Using `bypassSecurityTrustHtml` without verifying content is safe
- [ ] `[innerHTML]` binding with user-controlled content
- [ ] Constructing URLs with user input and `bypassSecurityTrustUrl`

**Expected behavior:** Let Angular sanitize by default; never bypass with user data.

### Section 2 — HTTP Security
- [ ] Not using Angular's `HttpClient` (loses XSRF protection)
- [ ] XSRF/CSRF token not configured with `HttpClientXsrfModule`
- [ ] HTTP interceptors not used for attaching auth tokens
- [ ] Sensitive data logged in HTTP interceptors

**Expected behavior:** HttpClient + XSRF module + auth interceptor.

### Section 3 — Route Guards
- [ ] `CanActivate` guards on the frontend only without server enforcement
- [ ] Auth state determined by a local storage flag instead of server validation
- [ ] Missing guards on lazy-loaded module routes

**Expected behavior:** Guards back UX; server enforces authz.

---

## Best Practices for This Stack

### Do
- Use Angular's `HttpClient`
- Configure the XSRF module
- Use guards plus server-side enforcement
- Let Angular sanitize by default

### Don't
- Bypass sanitization unless absolutely necessary
- Store auth state only in `localStorage`
- Skip route guards on lazy modules

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| HTML | Default sanitization | `bypassSecurityTrustHtml(userInput)` |
| HTTP | `HttpClient` + XSRF | Raw `fetch` |
| Auth token | Interceptor | Manual header per call |
| Guard | Server-backed | `localStorage` flag |
