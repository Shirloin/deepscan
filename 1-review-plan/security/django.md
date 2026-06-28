# Security — Django

> Read `_base.md` first. This file extends the base checklist with
> Django-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Django codebase.

---

## Framework-Specific Context

**Framework:** Django
**Type:** Backend
**Language:** Python
**Common patterns:** ORM, template autoescaping, settings, admin

---

## Django-Specific Checklist

### Section 1 — Django Security Settings
- [ ] `DEBUG=True` not controlled by environment variable
- [ ] `SECRET_KEY` hardcoded in `settings.py`
- [ ] `ALLOWED_HOSTS` set to `['*']` in production
- [ ] `SECURE_SSL_REDIRECT` not enabled in production
- [ ] `SESSION_COOKIE_SECURE` / `CSRF_COOKIE_SECURE` not set to `True`

**Expected behavior:** Hardened, environment-driven production settings.

### Section 2 — View and Template Security
- [ ] `mark_safe()` used with user-supplied content
- [ ] `{% autoescape off %}` in templates with user content
- [ ] Missing `@login_required` / `@permission_required` on protected views
- [ ] Object-level permissions not checked (accessing another user's data)
- [ ] `.raw()` / `extra()` with user input instead of parameterized ORM queries

**Expected behavior:** Autoescaped output, enforced object-level permissions.

### Section 3 — Django-Specific
- [ ] `CSRF_EXEMPT` used without documented justification
- [ ] File upload path traversal not prevented
- [ ] User-uploaded files served directly without content-type validation
- [ ] Django admin accessible without IP restriction in production

**Expected behavior:** CSRF enforced, safe uploads, restricted admin.

---

## Best Practices for This Stack

### Do
- Control `DEBUG` and `SECRET_KEY` via environment
- Use HTTPS-only cookie settings
- Use django-guardian for object permissions
- Set `ALLOWED_HOSTS` explicitly

### Don't
- Use `mark_safe` with user data
- Run raw queries with user input
- Leave admin open without restriction
- Disable autoescape in user-content templates

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Secret | From environment | Hardcoded |
| Output | Autoescaped | `mark_safe(user_input)` |
| Query | ORM parameterized | `.raw()` with f-string |
| Cookies | `Secure` + `HttpOnly` | Defaults |
