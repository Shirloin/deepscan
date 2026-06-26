# Architecture Review Checklist

When reviewing architecture, check every section in this file against the codebase.
Applies to all tech stacks: React, Vue, Angular, Next.js, Node.js, Python, Java, Go, PHP, and others.

For each violation found, record:
- **File** and **line number** (or module/folder when the issue is structural)
- **What the violation is**
- **Expected behavior** including a solution suggestion

---

## 1. Separation of Concerns

### 1.1 Business Logic in Wrong Layer
- [ ] Business logic written directly in route handlers or controllers
- [ ] Business rules embedded in database queries (complex logic inside stored procs that should be in app code)
- [ ] Calculation, transformation, or domain rules inside React/Vue components
- [ ] Sending emails, push notifications, or webhooks directly from a controller
- [ ] HTTP request/response objects (`req`, `res`) passed into service or domain layer functions

**Expected behavior:** Controllers handle HTTP only: parse request, call service, format response, return HTTP status. Services handle business logic: orchestrate domain operations, apply rules, call repositories. Repositories handle data access only. Domain/model layer holds core rules. Nothing crosses these boundaries.

### 1.2 Presentation Logic in Business Layer
- [ ] Service layer formatting data specifically for one frontend view
- [ ] Backend service returning HTML or UI-specific structures
- [ ] Business logic tightly coupled to a specific API contract or response shape

**Expected behavior:** Business logic should be UI-agnostic. Use serializers or presenters to shape data for specific consumers. The same service should be callable from REST, GraphQL, or a CLI without modification.

### 1.3 Data Access in Wrong Layer
- [ ] Database queries directly in route handlers
- [ ] Direct ORM calls in service functions that could use a repository abstraction
- [ ] SQL queries in view templates (classic PHP anti-pattern and equivalent in other stacks)
- [ ] Multiple layers all knowing about the database schema

**Expected behavior:** All database access through a repository or data access layer. Services call repositories, not ORM/DB clients directly. This allows swapping data stores and testing services in isolation.

---

## 2. Coupling

### 2.1 Tight Coupling Between Modules
- [ ] Module A imports deep internal files from Module B (`import { x } from '../moduleB/internal/utils'`)
- [ ] Two modules that always need to be changed together (shotgun surgery)
- [ ] Direct instantiation of dependencies inside classes instead of injection
- [ ] Feature modules importing directly from other feature modules without a shared interface

**Expected behavior:** Modules should expose a public API (index file) and hide internals. Other modules import only from the public API. Use dependency injection for services. Coupling should be through abstractions, not concrete implementations.

### 2.2 Circular Dependencies
- [ ] Module A imports from Module B which imports from Module A
- [ ] Service A depends on Service B which depends on Service A
- [ ] Circular imports causing initialization order issues

**Expected behavior:** Circular dependencies indicate a design flaw — usually that two modules are actually one, or that shared logic needs to be extracted to a third module that both depend on. Resolve by extracting shared logic or introducing an event/message pattern.

### 2.3 God Modules
- [ ] A single module, class, or service that everything else depends on
- [ ] A `utils.js` or `helpers.py` file with hundreds of unrelated functions
- [ ] A single service class with 20+ methods covering unrelated domains
- [ ] A single database model file that contains all business logic for the app

**Expected behavior:** Split large modules by domain or responsibility. A utility file should only contain utilities for one domain. A service class should handle one bounded context. Apply the Single Responsibility Principle at the module level.

---

## 3. API Design

### 3.1 HTTP Method Misuse
- [ ] `GET` endpoints that create, modify, or delete data
- [ ] `POST` used for everything instead of using PUT/PATCH/DELETE
- [ ] State-changing operations triggered by navigation (GET request side effects)

**Expected behavior:** Follow REST conventions: GET (read), POST (create), PUT (full update), PATCH (partial update), DELETE (remove). Side effects must never be triggered by GET requests.

### 3.2 Inconsistent Response Structure
- [ ] Some endpoints return `{ data: [...] }`, others return the array directly
- [ ] Error responses have different shapes across endpoints
- [ ] Some endpoints use camelCase, others use snake_case for field names
- [ ] Successful and error responses mixed in the same HTTP 200 response

**Expected behavior:** Standardize response envelope across all endpoints. Define one error response schema. Use consistent field naming (pick camelCase or snake_case, apply everywhere). Use correct HTTP status codes for success and error states.

### 3.3 Wrong HTTP Status Codes
- [ ] Returning 200 for errors
- [ ] Returning 500 for validation errors that should be 400
- [ ] Returning 200 with `{ success: false }` instead of a proper error status
- [ ] Not using 401 vs 403 correctly (401 = not authenticated, 403 = authenticated but not authorized)
- [ ] Using 404 when 403 would reveal the resource exists

**Expected behavior:** 200/201/204 for success. 400 for validation errors. 401 for unauthenticated. 403 for unauthorized. 404 for not found. 409 for conflicts. 422 for unprocessable entity. 500 for genuine server errors only.

### 3.4 Missing API Versioning
- [ ] Public API with no versioning (`/api/users` instead of `/api/v1/users`)
- [ ] Breaking changes deployed without a version increment
- [ ] No strategy for deprecating old API versions

**Expected behavior:** Version APIs from the start (`/v1/`, `/v2/`). Never make breaking changes to an existing version. Maintain old versions for a deprecation period with sunset headers. Document which versions are deprecated.

### 3.5 Missing Pagination
- [ ] List endpoints that return all records with no limit
- [ ] No pagination on endpoints that query potentially large tables
- [ ] Cursor or offset pagination not implemented for high-volume collections

**Expected behavior:** All list endpoints must support pagination. Default page size should be reasonable (20–100). Provide total count or next-page cursor. Never allow unbounded queries to production data.

---

## 4. Design Patterns and Principles

### 4.1 Missing Abstraction Layers
- [ ] Direct third-party SDK calls scattered throughout business logic
- [ ] Email provider, SMS provider, payment gateway accessed directly in multiple services
- [ ] No wrapper or adapter around external services
- [ ] Switching providers would require changes across the entire codebase

**Expected behavior:** Wrap all external services behind an interface/abstraction layer. `EmailService` that internally uses SendGrid today but could use SES tomorrow without changing callers. This also makes unit testing possible by swapping real implementations with mocks.

### 4.2 Inconsistent Patterns
- [ ] Some parts of the codebase use async/await, others use callbacks, others use raw Promises
- [ ] Some modules use class-based design, others use functional, with no clear rule
- [ ] Some features have tests, others don't — no consistent testing approach
- [ ] Multiple ways to do the same thing in the same codebase (3 different HTTP client libraries)

**Expected behavior:** Establish and document patterns for the codebase. Pick one async pattern and use it consistently. Pick one HTTP client. Pick one testing approach. Enforce via linter rules or code review checklists.

### 4.3 Anemic Domain Model
- [ ] Domain objects (User, Order, Product) are pure data containers with no behavior
- [ ] All business logic for a domain object scattered across service files
- [ ] Methods like `OrderService.calculateTotal(order)` instead of `order.calculateTotal()`

**Expected behavior:** Domain objects should encapsulate their own behavior. An `Order` should know how to calculate its total, apply a discount, and validate its state. Services orchestrate domain objects — they don't implement domain logic themselves.

### 4.4 Missing Repository Pattern (Backend)
- [ ] ORM queries (Sequelize, Prisma, SQLAlchemy, Hibernate) called directly in service files
- [ ] Same query written in multiple services
- [ ] Switching databases would require changes throughout business logic

**Expected behavior:** Wrap all data access in repository classes/functions. `UserRepository.findByEmail()` instead of `User.findOne({ where: { email } })` directly in service code. Repositories can be mocked for testing and swapped for different data sources.

---

## 5. Frontend Architecture (React, Vue, Angular, Next.js)

### 5.1 No Feature-Based Structure
- [ ] All components in one flat `components/` folder
- [ ] No grouping by feature, domain, or page
- [ ] Shared and feature-specific components mixed together
- [ ] Growing codebase with no clear way to find where a feature lives

**Expected behavior:** Organize by feature or domain, not by file type. `features/auth/`, `features/checkout/`, `features/profile/` — each with its own components, hooks, services, and tests. Shared utilities go in `shared/` or `common/`.

### 5.2 Prop Drilling
- [ ] Props passed through 3+ component levels to reach the component that needs them
- [ ] Intermediate components receiving props they don't use, only to pass them down
- [ ] Adding a new prop requires changing multiple component signatures

**Expected behavior:** Use React Context, Vue provide/inject, Angular services, or a state management library (Zustand, Pinia, NgRx) for data needed across multiple levels. Props are for parent-to-direct-child communication only.

### 5.3 Business Logic in Components
- [ ] API calls made directly inside component lifecycle methods with no abstraction
- [ ] Data transformation logic inside component render functions
- [ ] Form validation rules embedded in component code instead of a schema
- [ ] Business rules duplicated across multiple components

**Expected behavior:** Extract API calls to service files or custom hooks. Extract transformation logic to pure utility functions. Extract validation to schema libraries (Zod, Yup). Components should be as thin as possible — receive data and render it.

### 5.4 Missing Error Boundaries (React)
- [ ] No error boundaries around major UI sections
- [ ] A JavaScript error in one component crashing the entire app
- [ ] No fallback UI for async loading or error states

**Expected behavior:** Wrap major sections with error boundaries. Every async data dependency should have loading, error, and empty states handled explicitly.

---

## 6. Observability and Maintainability

### 6.1 No Structured Logging
- [ ] Using `console.log` as the logging strategy with no log levels
- [ ] Log messages with no context (timestamp, request ID, user ID, operation)
- [ ] Different log formats in different parts of the codebase
- [ ] No correlation ID to trace a request across multiple services

**Expected behavior:** Use a structured logging library (Winston, Pino, structlog, Log4j). Define log levels (debug, info, warn, error). Include request ID, user ID, and operation context in every log. Use consistent JSON format for machine-parseable logs.

### 6.2 No Health Check Endpoint
- [ ] No `/health` or `/ping` endpoint for load balancers and monitoring
- [ ] Health check doesn't verify database connectivity
- [ ] No readiness vs liveness distinction for containerized deployments

**Expected behavior:** Implement a `/health` endpoint that checks: app is running, database is reachable, critical external services are reachable. Return 200 when healthy, 503 when not. For Kubernetes: separate `/ready` and `/live` endpoints.

### 6.3 No Audit Trail for Sensitive Operations
- [ ] No logging of who performed what sensitive action (login, data export, admin action, payment)
- [ ] No timestamp on sensitive operations
- [ ] Audit logs stored in the same database as application data (can be tampered with)

**Severity:** High
**Expected behavior:** Log all sensitive operations with: who (user ID), what (action), when (timestamp), on what (resource ID), from where (IP). Store audit logs separately from application data. Never allow audit logs to be deleted by the application.

---

## 7. Configuration Management

### 7.1 No Environment-Specific Configuration
- [ ] Config values hardcoded for one environment
- [ ] No separation between development, staging, and production config
- [ ] Feature flags hardcoded in code instead of config

**Expected behavior:** All environment-specific values from environment variables or a config service. Use `.env.development`, `.env.staging`, `.env.production` pattern or a config management service (AWS Parameter Store, Vault).

### 7.2 No Validation of Required Config
- [ ] App starts successfully even when required environment variables are missing
- [ ] Missing config causes runtime errors far from startup instead of a clear startup failure
- [ ] No documentation of required environment variables

**Expected behavior:** Validate all required config at startup. If any required config is missing, fail fast with a clear error listing what's missing. Use a schema for config validation (Joi, Zod, Pydantic Settings). Document all required and optional env vars.