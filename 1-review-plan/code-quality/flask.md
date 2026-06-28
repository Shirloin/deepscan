# Code Quality — Flask

> Read `_base.md` first. This file extends the base checklist with
> Flask-specific patterns, anti-patterns, and best practices.
> Apply BOTH this file AND `_base.md` when reviewing a Flask codebase.

---

## Framework-Specific Context

**Framework:** Flask
**Type:** Backend
**Language:** Python
**Common patterns:** Blueprints, application factory, extensions, WSGI

---

## Flask-Specific Checklist

### Section 1 — Application Structure
- [ ] All routes in one file with no blueprints
- [ ] Not using Flask Blueprints for modular organization
- [ ] No Application Factory pattern (`create_app()` function)
- [ ] Configuration not using Flask config classes
- [ ] Not using extensions properly (SQLAlchemy, Marshmallow, etc.)

**Expected behavior:** Factory + blueprints + config classes.

### Section 2 — Route and View Quality
- [ ] Business logic directly in route functions
- [ ] Database queries in route functions without a service layer
- [ ] Missing input validation (not using Flask-WTF, Marshmallow, or Pydantic)
- [ ] Returning raw dictionaries instead of proper JSON responses with status codes
- [ ] No error handlers registered with `@app.errorhandler`

**Expected behavior:** Thin views, validated input, registered error handlers.

### Section 3 — Flask-Specific Patterns
- [ ] Using global variables instead of Flask's `g` or application context
- [ ] Not using Flask-Login for authentication management
- [ ] Secret key hardcoded instead of from environment variable
- [ ] `DEBUG=True` hardcoded instead of from environment variable
- [ ] Not using `current_app` proxy inside application context

**Expected behavior:** Proper context usage, environment-driven config.

---

## Best Practices for This Stack

### Do
- Use the Application Factory pattern
- Use Blueprints for modularity
- Use Marshmallow for serialization
- Use Flask-Login for auth
- Use config classes for settings

### Don't
- Put all routes in one file
- Hardcode the secret key or debug mode
- Put business logic in routes
- Skip input validation

---

## Common Patterns to Look For

| Pattern | Good | Bad |
|---|---|---|
| App setup | `create_app()` factory | Module-level `app = Flask()` |
| Modularity | Blueprints | One giant routes file |
| State | `g` / app context | Module globals |
| Config | Config class + env | Hardcoded values |
