# Security — Vue

> Read `_base.md` first. This file extends the base checklist with
> Vue-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Vue codebase.

---

## Framework-Specific Context

**Framework:** Vue
**Type:** Frontend
**Language:** JavaScript / TypeScript
**Common patterns:** Template interpolation escaping, Pinia/Vuex, Vue Router

---

## Vue-Specific Checklist

### Section 1 — XSS Risks
- [ ] `v-html` directive used with user-controlled content
- [ ] User input interpolated without sanitization in templates
- [ ] Dynamic component rendering with user-controlled component names

**Expected behavior:** Avoid `v-html` with user data; sanitize when required.

### Section 2 — Auth and State
- [ ] Vuex/Pinia store containing auth tokens accessible via Vue DevTools
- [ ] Route guards not backed by server-side enforcement
- [ ] Sensitive data in store logged by devtools in production

**Expected behavior:** Server enforces authz; devtools disabled in production.

### Section 3 — API Security
- [ ] Axios interceptors not used for auth token attachment
- [ ] No CSRF protection for cookie-based auth
- [ ] API base URL hardcoded instead of from environment variable

**Expected behavior:** Interceptor-attached tokens, CSRF protection, env config.

---

## Best Practices for This Stack

### Do
- Avoid `v-html` with user data; use DOMPurify when HTML rendering is needed
- Enforce auth server-side
- Disable Vue DevTools in production

### Don't
- Use `v-html` with untrusted content
- Rely on frontend-only route guards
- Store sensitive data in Vuex without considering DevTools exposure

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| HTML | Escaped `{{ }}` / DOMPurify | `v-html` with user data |
| Auth token | Axios interceptor | Manual per request |
| Route guard | Server-backed | Client only |
| Base URL | Env variable | Hardcoded |
