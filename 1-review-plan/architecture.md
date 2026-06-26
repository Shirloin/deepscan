# Architecture Review Checklist

When reviewing architecture, check for every item in this list.
For each issue found, record: file path or module, description, severity.

## Separation of Concerns
- [ ] Business logic mixed directly into route handlers or controllers
- [ ] Database queries written directly in view/template files
- [ ] Presentation logic mixed into data access layers
- [ ] Validation logic scattered across multiple layers instead of centralized
- [ ] Email/notification sending mixed into business logic functions

**Severity guide:** High if it makes the codebase untestable, Medium otherwise

## Coupling
- [ ] Modules directly importing from deep internal paths of other modules
- [ ] Circular dependencies between modules or services
- [ ] Hard dependencies on concrete implementations instead of abstractions
- [ ] Shared mutable global state between unrelated modules
- [ ] Direct database access from multiple unrelated modules

**Severity guide:** High if circular, Medium for excessive coupling

## API Design
- [ ] Inconsistent HTTP method usage (GET requests that modify data)
- [ ] Missing or inconsistent API versioning
- [ ] Wrong HTTP status codes returned (200 for errors, etc.)
- [ ] Inconsistent response structures across endpoints
- [ ] Overly large response payloads returning unnecessary data
- [ ] Missing pagination on endpoints that could return large datasets
- [ ] Breaking changes introduced without versioning

**Severity guide:** High for GET modifying data, Medium for inconsistency

## Scalability
- [ ] In-memory state that won't work across multiple instances
- [ ] File system writes that assume single-server deployment
- [ ] Cron jobs that would run on every instance in a multi-instance setup
- [ ] Missing queue for long-running or async operations
- [ ] Synchronous processing of tasks that should be async

**Severity guide:** High if blocking scale-out, Medium for single-instance assumptions

## Observability
- [ ] No structured logging (plain text logs with no levels or context)
- [ ] Missing request ID or correlation ID in logs
- [ ] No metrics or health check endpoints
- [ ] Errors caught and silenced with no logging
- [ ] No audit trail for sensitive operations (login, data changes, admin actions)

**Severity guide:** High for missing audit trails on sensitive ops, Medium for missing logs

## Dependency Management
- [ ] Business logic depending directly on third-party SDK internals
- [ ] No abstraction layer around external services (email, SMS, payments)
- [ ] Multiple different libraries doing the same job (3 HTTP clients, 2 loggers)
- [ ] Vendored dependencies out of sync with declared versions

**Severity guide:** Medium, High if makes testing or migration impossible
