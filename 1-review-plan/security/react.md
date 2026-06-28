# Security — React

> Read `_base.md` first. This file extends the base checklist with
> React-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a React codebase.

---

## Framework-Specific Context

**Framework:** React
**Type:** Frontend
**Language:** JavaScript / TypeScript
**Common patterns:** Client-rendered SPA, JSX escaping, token-based auth

---

## React-Specific Checklist

### Section 1 — XSS Prevention
- [ ] `dangerouslySetInnerHTML` used with unsanitized user content
- [ ] User input rendered without escaping in JSX expressions
- [ ] URL values from user input set on `href`/`src` without validation (`javascript:`)
- [ ] Third-party scripts injected via `innerHTML`

**Expected behavior:** Sanitize rich HTML with DOMPurify; validate URL protocols.

### Section 2 — Sensitive Data
- [ ] API keys or secrets in source code or committed `.env` files
- [ ] Sensitive data stored in `localStorage` / `sessionStorage` (XSS accessible)
- [ ] JWT tokens stored in `localStorage` instead of httpOnly cookies
- [ ] PII in Redux/Zustand state logged to console or error tracking

**Expected behavior:** Secrets stay server-side; tokens in httpOnly cookies.

### Section 3 — Auth and Authorization
- [ ] Route protection implemented only in the frontend (no server-side check)
- [ ] Hiding UI elements as the only access control (no API enforcement)
- [ ] Auth tokens not cleared properly on logout
- [ ] Token expiry not handled — expired token silently fails or crashes

**Expected behavior:** Server enforces all authz; frontend guards are UX only.

---

## Best Practices for This Stack

### Do
- Use DOMPurify for rich HTML
- Store tokens in httpOnly cookies
- Enforce auth server-side
- Validate URL protocols before setting `href`

### Don't
- Use `dangerouslySetInnerHTML` with user data
- Store tokens in `localStorage`
- Rely on frontend-only route guards

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Rich HTML | DOMPurify sanitized | `dangerouslySetInnerHTML` raw |
| Token storage | httpOnly cookie | `localStorage` |
| Route guard | Server + client | Client only |
| Secrets | Server-side env | Hardcoded in bundle |
