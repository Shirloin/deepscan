# Security — Laravel

> Read `_base.md` first. This file extends the base checklist with
> Laravel-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Laravel codebase.

---

## Framework-Specific Context

**Framework:** Laravel
**Type:** Backend
**Language:** PHP
**Common patterns:** Eloquent, Blade escaping, policies, middleware

---

## Laravel-Specific Checklist

### Section 1 — Laravel Security Features
- [ ] Mass assignment vulnerability — missing `$fillable`/`$guarded`
- [ ] CSRF protection disabled for routes that need it
- [ ] SQL injection via raw queries: `DB::select("SELECT * WHERE id = $id")`
- [ ] Missing authentication middleware on protected route groups
- [ ] Authorization policies not used — manual role checks in controllers

**Expected behavior:** Guarded models, CSRF on, parameterized queries, policies.

### Section 2 — Data and Output
- [ ] User content rendered with `{!! !!}` (unescaped) instead of `{{ }}`
- [ ] File uploads stored in `public/` directly accessible
- [ ] User-supplied filenames used for stored files (path traversal risk)
- [ ] Sensitive data returned in API responses without `$hidden`

**Expected behavior:** Escaped output, safe file storage, hidden fields.

### Section 3 — Laravel-Specific
- [ ] `APP_KEY` not rotated or hardcoded
- [ ] `APP_DEBUG=true` in production `.env`
- [ ] Telescope or Debugbar enabled in production
- [ ] Rate limiting not configured on auth routes (`throttle` missing)

**Expected behavior:** Secure app key, debug off, throttled auth.

---

## Best Practices for This Stack

### Do
- Set `$fillable` on all models
- Use Policy classes for authorization
- Use `{{ }}` for all output
- Store uploaded files outside `public/`
- Use `throttle` middleware on auth

### Don't
- Use `{!! !!}` with user data
- Run raw queries with variables
- Omit `$fillable`
- Skip authorization policies

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| Mass assignment | `$fillable` set | Open model |
| Output | `{{ $x }}` | `{!! $x !!}` user data |
| Query | Eloquent / bindings | Interpolated raw SQL |
| Authz | Policy | Manual role check |
