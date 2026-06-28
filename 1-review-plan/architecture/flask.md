# Architecture — Flask

> Read `_base.md` first. This file extends the base checklist with
> Flask-specific architectural concerns.
> Apply BOTH this file AND `_base.md` when reviewing a Flask codebase.

---

## Framework-Specific Context

**Framework:** Flask
**Type:** Backend
**Language:** Python
**Common patterns:** Blueprints, application factory, extensions, config classes

---

## Flask-Specific Checklist

### Section 1 — Blueprint and Factory Organization
- [ ] Blueprints not organized by feature
- [ ] Application Factory pattern not used
- [ ] App created at import time instead of via `create_app()`

**Expected behavior:** Feature blueprints created in an app factory.

### Section 2 — Extensions and Service Layer
- [ ] Extensions initialized inconsistently (not with `init_app`)
- [ ] No service layer; logic in views
- [ ] SQLAlchemy models and repositories not organized

**Expected behavior:** Extensions via `init_app`; service layer present.

### Section 3 — Configuration
- [ ] No configuration class hierarchy
- [ ] Environment differences handled ad hoc
- [ ] Config values scattered across modules

**Expected behavior:** Config class hierarchy selected by environment.

---

## Best Practices for This Stack

### Do
- Organize blueprints by feature
- Use the Application Factory pattern
- Initialize extensions with `init_app`
- Use a config class hierarchy

### Don't
- Create the app at import time
- Put business logic in views
- Scatter configuration

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| App | `create_app()` factory | Module-level app |
| Blueprints | By feature | One routes module |
| Extensions | `init_app` | Bound at import |
| Config | Class hierarchy | Scattered literals |
