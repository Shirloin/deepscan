# Security — Flask

> Read `_base.md` first. This file extends the base checklist with
> Flask-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Flask codebase.

---

## Framework-Specific Context

**Framework:** Flask
**Type:** Backend
**Language:** Python
**Common patterns:** Jinja2 templating, extensions, WSGI, sessions

---

## Flask-Specific Checklist

### Section 1 — Flask Security Settings
- [ ] `SECRET_KEY` hardcoded instead of from environment variable
- [ ] `DEBUG=True` hardcoded in application code
- [ ] Session cookies not configured as secure and httpOnly
- [ ] No CSRF protection (Flask-WTF CSRF not enabled)

**Expected behavior:** Env-driven secrets, secure cookies, CSRF on.

### Section 2 — Input and Output
- [ ] `render_template_string()` used with user input (SSTI risk)
- [ ] `Markup()` used with unsanitized user content (XSS risk)
- [ ] SQL queries via string formatting with user input
- [ ] File uploads without content-type and extension validation

**Expected behavior:** No template injection, parameterized queries, safe uploads.

### Section 3 — Flask-Specific
- [ ] No rate limiting on auth/sensitive endpoints (Flask-Limiter not used)
- [ ] `@login_required` missing on protected routes
- [ ] Error handlers not registered — default pages expose info
- [ ] User-uploaded files served from the same domain as the app

**Expected behavior:** Rate-limited, protected routes; custom error handlers.

---

## Best Practices for This Stack

### Do
- Control `SECRET_KEY` and `DEBUG` via environment
- Use Flask-WTF for CSRF
- Use Flask-Login for auth
- Use Flask-Limiter for rate limiting
- Register custom error handlers

### Don't
- Use `render_template_string` with user input
- Hardcode secrets
- Serve uploads from the app domain
- Skip CSRF protection

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Secret | Env variable | Hardcoded |
| Template | Static template file | `render_template_string(user)` |
| Query | Parameterized | `% ` / f-string SQL |
| CSRF | Flask-WTF enabled | Disabled |
