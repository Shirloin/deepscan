# Code Quality Review Checklist

When reviewing code quality, check every section in this file against the codebase.
Applies to all tech stacks: React, Vue, Angular, Next.js, Node.js, Python, Java, Go, PHP, and others.

For each violation found, record:
- **File** and **line number**
- **What the violation is**
- **Expected behavior** including a solution suggestion

---

## 1. DRY — Don't Repeat Yourself

### 1.1 Duplicated Logic Blocks
- [ ] Same conditional logic written in 2+ different files
- [ ] Same data transformation written multiple times (e.g. date formatting, price calculation)
- [ ] Same validation rules duplicated across forms, controllers, or services
- [ ] Identical error handling blocks copy-pasted across functions
- [ ] Same API call with same params written in multiple components or services

**Expected behavior:** Extract duplicated logic into a shared utility function, hook, service, or helper. Import it wherever needed. One source of truth for one piece of logic.

### 1.2 Duplicated Constants
- [ ] Same string literal (URL, status code, role name, config key) written in multiple places
- [ ] Same magic number used in multiple files without a named constant
- [ ] Environment-specific values hardcoded in multiple locations

**Expected behavior:** Define constants in a single `constants.js/ts/py` file or config module. Import everywhere needed. Changing one value should require editing one place only.

### 1.3 Duplicated Component or Template Structure
- [ ] (Frontend) Same JSX/HTML structure repeated across multiple components with minor differences
- [ ] Same table, card, or form layout duplicated instead of extracted into a reusable component
- [ ] Same CSS class combinations repeated instead of a shared component or utility class

**Expected behavior:** Extract repeated UI patterns into a parameterized component with props/inputs for variation. Reuse the component everywhere.

---

## 2. SOLID Principles

### 2.1 Single Responsibility Principle (SRP)
- [ ] A function, class, or component doing more than one distinct job
- [ ] A service that handles business logic AND database access AND HTTP calls
- [ ] A React/Vue component that fetches data, transforms it, AND renders complex UI
- [ ] A function that both validates input AND saves to the database
- [ ] A module that handles authentication AND sends email notifications

**Expected behavior:** Each function, class, or component should have one reason to change. Split into smaller focused units — separate data fetching from rendering, separate business logic from persistence logic.

### 2.2 Open/Closed Principle (OCP)
- [ ] Adding a new feature requires modifying existing functions with new `if/else` or `switch` cases
- [ ] A function with a growing list of conditionals based on a type or role string
- [ ] Feature flags or type checks scattered throughout business logic

**Expected behavior:** Design for extension, not modification. Use polymorphism, strategy pattern, or configuration-driven behavior so new types or behaviors can be added without touching existing code.

### 2.3 Liskov Substitution Principle (LSP)
- [ ] A subclass overriding a parent method with completely different behavior or return type
- [ ] A child class throwing errors for methods it "doesn't support" from the parent
- [ ] TypeScript/Java interface implementations that only partially implement the contract

**Expected behavior:** Subclasses or implementations should be substitutable for their parent/interface without breaking behavior. If substitution causes errors, the inheritance hierarchy is wrong — favor composition.

### 2.4 Interface Segregation Principle (ISP)
- [ ] A large interface or base class forcing implementors to define methods they don't use
- [ ] Functions accepting large object params but only using 1-2 fields
- [ ] Components receiving large prop objects but only consuming a fraction of them

**Expected behavior:** Split large interfaces into smaller specific ones. Pass only what a function or component actually needs — use object destructuring or smaller focused types.

### 2.5 Dependency Inversion Principle (DIP)
- [ ] High-level business logic directly instantiating low-level services or libraries
- [ ] Hardcoded database client, email sender, or HTTP client inside business logic
- [ ] No dependency injection — services creating their own dependencies internally
- [ ] Direct imports of concrete implementations in places that should use abstractions

**Expected behavior:** Business logic should depend on abstractions (interfaces, abstract classes, injected dependencies), not concrete implementations. Inject dependencies from outside. This makes units testable in isolation.

---

## 3. Hardcoding

### 3.1 Hardcoded Credentials and Secrets
- [ ] API keys, tokens, passwords hardcoded in source files
- [ ] Database connection strings with credentials in code
- [ ] Third-party service keys (Stripe, AWS, Twilio, etc.) in code
- [ ] Any string that looks like a secret (long alphanumeric, starts with `sk_`, `pk_`, `AIza`, etc.)

**Severity:** Critical — auto-confirmed
**Expected behavior:** All secrets must come from environment variables or a secrets manager (AWS Secrets Manager, HashiCorp Vault, etc.). Use `.env` files locally with `.env` in `.gitignore`. Never commit secrets.

### 3.2 Hardcoded URLs and Endpoints
- [ ] Production API URLs hardcoded in frontend or backend code
- [ ] Internal service URLs hardcoded instead of using config or service discovery
- [ ] Hardcoded `localhost` URLs that will break in non-local environments
- [ ] Hardcoded port numbers scattered across the codebase

**Expected behavior:** All URLs and endpoints must come from environment config (`process.env.API_URL`, `os.environ`, application.properties, etc.). One place to change per environment.

### 3.3 Hardcoded Business Values
- [ ] Tax rates, discount percentages, or pricing values in code
- [ ] Role names or permission strings written as literals in conditionals
- [ ] Status codes or state machine transitions as magic strings
- [ ] Timeout or retry values scattered across multiple files
- [ ] Pagination sizes or limits hardcoded in multiple places

**Expected behavior:** Business rules that may change belong in config files, database-driven config, or named constants. Code should not need to change when a business value changes.

### 3.4 Hardcoded File Paths
- [ ] Absolute file paths (`/home/user/project/uploads`) in code
- [ ] OS-specific path separators hardcoded (will break cross-platform)

**Expected behavior:** Use path utilities (`path.join`, `os.path.join`, `Paths.get`) and environment-relative paths. Never hardcode absolute paths.

---

## 4. Naming Conventions

### 4.1 Misleading Names
- [ ] Function named `getUser` that also writes to the database
- [ ] Function named `isValid` that returns an object, not a boolean
- [ ] Variable named `data` or `result` with no indication of what it contains
- [ ] Boolean variable not named as a question (`active` vs `isActive`, `enabled` vs `hasPermission`)
- [ ] Array variable with singular name (`user` holding a list of users)

**Expected behavior:** Names should describe what a thing IS or DOES exactly. Functions that return booleans: `isX`, `hasX`, `canX`. Arrays: plural names. Functions: verb phrases describing the action.

### 4.2 Inconsistent Conventions
- [ ] Mixed casing conventions in the same file or module (`camelCase` and `snake_case` for same language)
- [ ] Some components use PascalCase, others use kebab-case in the same project
- [ ] Database column names don't match object property names without a mapping layer
- [ ] Some files use named exports, others use default exports inconsistently

**Expected behavior:** Pick one convention per context and apply it everywhere. Document it in a style guide or enforce via linter rules.

### 4.3 Overly Generic Names
- [ ] Functions or variables named `handle`, `process`, `manage`, `do`, `run` with no further context
- [ ] Classes named `Manager`, `Handler`, `Processor`, `Helper`, `Utils` as the entire name
- [ ] Parameters named `obj`, `arr`, `val`, `temp`, `x`

**Expected behavior:** Names should be specific enough to understand without reading the implementation. `processPaymentRefund` is better than `process`. `userAuthenticationService` is better than `authHelper`.

---

## 5. Function and Class Complexity

### 5.1 Functions Too Long
- [ ] Functions exceeding 40–50 lines (excluding comments)
- [ ] Functions that need multiple screens to read
- [ ] A single function handling multiple distinct phases of a workflow

**Expected behavior:** Extract logical sub-steps into named helper functions. Each function should be readable in one screen and describe one step. The main function orchestrates; helpers execute.

### 5.2 Too Many Parameters
- [ ] Functions with 4+ positional parameters
- [ ] Constructor with 5+ parameters
- [ ] Multiple boolean flags passed to a function controlling behavior

**Expected behavior:** Group related parameters into a config object or data class. Replace boolean flags with separate functions or a strategy pattern. Use named parameters where the language supports it.

### 5.3 Deep Nesting
- [ ] More than 3 levels of nested `if/else` blocks
- [ ] Callback hell — deeply nested async callbacks
- [ ] Deeply nested ternary expressions
- [ ] Nested loops over nested objects 4+ levels deep

**Expected behavior:** Use early returns (guard clauses) to flatten conditionals. Use `async/await` to flatten callback chains. Extract nested logic into named functions. Aim for maximum 2 levels of nesting.

### 5.4 High Cyclomatic Complexity
- [ ] Functions with 10+ distinct branches (if/else, switch cases, loops, catches)
- [ ] Switch statements with 10+ cases in business logic
- [ ] Functions that are hard to unit test because of too many code paths

**Expected behavior:** Break high-complexity functions into smaller units. Each branch should be a named function or strategy. Complexity above 10 in a single function is a strong refactor signal.

---

## 6. Error Handling

### 6.1 Silent Failures
- [ ] Empty `catch` blocks: `catch (e) {}`
- [ ] Caught errors logged but not propagated when the caller needs to know
- [ ] Promises with `.catch(() => {})` swallowing errors
- [ ] Try/catch around entire function body hiding which specific operation failed

**Severity:** High when in critical paths (auth, payments, data writes)
**Expected behavior:** Every caught error should be logged with context AND either rethrown, returned as an error result, or handled with a specific recovery action. Never silently discard errors.

### 6.2 No Error Handling at All
- [ ] Async operations with no try/catch or `.catch()`
- [ ] External API calls with no error handling
- [ ] Database queries with no error handling
- [ ] File system operations with no error handling
- [ ] JSON.parse without try/catch

**Severity:** High
**Expected behavior:** All operations that can fail must have explicit error handling. For async code, every `await` in a critical path should be inside a try/catch or use a result wrapper pattern.

### 6.3 Poor Error Messages
- [ ] Throwing `new Error("error")` or `new Error("something went wrong")`
- [ ] Error messages with no context about what failed or why
- [ ] Exposing raw database errors or stack traces to API consumers

**Expected behavior:** Error messages should include: what operation failed, what the input was (safely), and what the caller can do about it. Never expose internal error details to API responses — log internally, return sanitized messages externally.

---

## 7. Code Hygiene

### 7.1 Debug Code Left In
- [ ] `console.log`, `print()`, `System.out.println()`, `var_dump()` in production code
- [ ] `debugger` statements
- [ ] `TODO` or `FIXME` comments older than the current sprint in critical files
- [ ] Commented-out code blocks left without explanation

**Expected behavior:** Remove all debug statements before merging. If a TODO is important, it should be a tracked issue, not a code comment. Remove commented-out code — version control preserves history.

### 7.2 Dead Code
- [ ] Functions defined but never called anywhere in the codebase
- [ ] Variables declared but never read
- [ ] Imported modules never used
- [ ] Entire files that are never imported or referenced
- [ ] Feature flags that are always `true` or always `false`
- [ ] `else` branches that can never be reached

**Expected behavior:** Delete dead code. Rely on version control for history. Dead code increases cognitive load and maintenance burden with zero benefit.

### 7.3 Inconsistent Formatting
- [ ] Mixed indentation (tabs and spaces in the same file)
- [ ] Inconsistent quote styles in the same file without a linter enforcing it
- [ ] No linter or formatter configured for the project
- [ ] Files that differ significantly in style from the rest of the codebase

**Expected behavior:** Every project should have a linter (ESLint, Pylint, Checkstyle) and formatter (Prettier, Black, gofmt) configured and enforced in CI. Formatting should never be a code review discussion.

---

## 8. Frontend-Specific Quality (React, Vue, Angular, Next.js)

### 8.1 Component Design
- [ ] Components exceeding 200–300 lines
- [ ] Components handling data fetching, state management, AND complex rendering all in one
- [ ] No separation between presentational (dumb) and container (smart) components
- [ ] Deeply nested component trees passing props 3+ levels down (prop drilling)
- [ ] Direct DOM manipulation instead of reactive state

**Expected behavior:** Split large components into smaller focused ones. Use state management or context/provide for deeply nested state. Separate data concerns from rendering concerns.

### 8.2 State Management
- [ ] Global state used for data that is local to one component
- [ ] Local component state used for data shared across multiple components
- [ ] Unnecessary re-renders due to state defined at wrong level
- [ ] State mutations instead of immutable updates

**Expected behavior:** Co-locate state as close to where it's used as possible. Use global state only for truly shared data. Always update state immutably.

### 8.3 Hooks and Lifecycle (React)
- [ ] Missing dependency arrays in `useEffect`
- [ ] Infinite loops caused by incorrect `useEffect` dependencies
- [ ] API calls without cleanup/abort on unmount
- [ ] Using `useEffect` for things that don't need it (pure derived values)
- [ ] Too many `useEffect` hooks in one component

**Expected behavior:** Every `useEffect` should have an explicit dependency array. Long-running effects should return a cleanup function. Derived values should use `useMemo` or be computed inline.

### 8.4 Accessibility (a11y)
- [ ] Interactive elements (`div`, `span`) used instead of semantic HTML (`button`, `a`, `input`)
- [ ] Images missing `alt` attributes
- [ ] Forms missing label associations
- [ ] No keyboard navigation support for custom interactive components
- [ ] Color contrast issues in custom styled components

**Expected behavior:** Use semantic HTML elements. Add ARIA attributes where semantic HTML isn't sufficient. Every interactive element must be keyboard accessible.

---

## 9. Backend-Specific Quality (Node.js, Python, Java, Go, PHP)

### 9.1 Service Layer
- [ ] Business logic written directly in route handlers or controllers
- [ ] Database queries in route handlers without a service or repository layer
- [ ] HTTP request/response objects passed into business logic services
- [ ] No clear separation between controllers, services, and data access layers

**Expected behavior:** Controllers handle HTTP concerns only (parse request, call service, format response). Services handle business logic. Repositories/DAOs handle data access. Never mix layers.

### 9.2 Input Validation
- [ ] Request body used directly without validation
- [ ] Trusting client-supplied types without validation
- [ ] No schema validation on incoming data (Joi, Zod, Pydantic, Bean Validation, etc.)
- [ ] Validation logic spread across multiple layers instead of centralized at the entry point

**Expected behavior:** Validate and sanitize ALL input at the entry point (route handler or controller) before passing to business logic. Use a schema validation library. Reject invalid input early with clear error messages.

### 9.3 Async Patterns (Node.js)
- [ ] Mixing callbacks and Promises in the same codebase
- [ ] Unhandled promise rejections
- [ ] Blocking the event loop with synchronous operations (sync file reads, CPU-heavy loops)
- [ ] Missing `await` on async function calls

**Expected behavior:** Use `async/await` consistently. Never use synchronous I/O in request handlers. Handle all promise rejections. Use worker threads for CPU-intensive operations.

---

## 10. File and Directory Structure

### 10.1 Wrong File in Wrong Folder
- [ ] A service file placed inside a `components/` folder
- [ ] A utility function inside a `pages/` or `routes/` folder
- [ ] A database model inside a `controllers/` folder
- [ ] Test files mixed inside source folders with no clear separation
- [ ] Configuration files scattered across multiple directories

**Expected behavior:** Every file should live in a folder that matches its responsibility. Controllers in `controllers/`, services in `services/`, models in `models/`, utilities in `utils/` or `lib/`. For feature-based structures: `features/<name>/components/`, `features/<name>/services/`. A developer should be able to find any file by knowing its type and domain without searching.

### 10.2 No Feature or Domain Grouping
- [ ] All files of the same type in one flat folder regardless of domain (`components/UserCard.jsx`, `components/ProductCard.jsx`, `components/OrderTable.jsx` all flat)
- [ ] Growing codebase with 50+ files in one directory
- [ ] No clear way to find all files related to one feature
- [ ] Cross-feature dependencies making it impossible to isolate features

**Expected behavior:** Group by feature or domain at the top level, then by type within each feature. `features/auth/`, `features/checkout/`, `features/user-profile/`. Each feature folder is self-contained with its own components, hooks, services, tests. Shared utilities go in `shared/` or `common/`.

### 10.3 Inconsistent File Naming
- [ ] Mix of `camelCase.js`, `PascalCase.js`, and `kebab-case.js` for the same type of file
- [ ] Component files not matching their exported component name (`userCard.jsx` exporting `UserCard`)
- [ ] Test files using mixed conventions (`user.test.js`, `userSpec.js`, `test_user.py`)
- [ ] Index files named inconsistently (`index.js` in some folders, `main.js` in others)
- [ ] Files named after their type rather than their purpose (`helper.js`, `utils.js`, `service.js` with no domain prefix)

**Expected behavior:** Pick one file naming convention and apply it everywhere per file type. Components: `PascalCase.tsx`. Utilities: `camelCase.ts` or `kebab-case.ts`. Tests: `<filename>.test.ts` or `<filename>.spec.ts` matching the source file name exactly. Enforce via linter configuration.

### 10.4 Bloated Index Files
- [ ] `index.js` containing actual business logic instead of just re-exports
- [ ] A single index file that imports and re-exports hundreds of items
- [ ] Index files containing conditional logic or side effects on import

**Expected behavior:** Index files should only re-export. `export { UserService } from './UserService'`. Never put logic in index files. If an index file is doing too much, the folder structure needs to be rethought.

### 10.5 Dead Files
- [ ] Files that exist in the codebase but are never imported anywhere
- [ ] Old feature folders left after a feature was removed
- [ ] Duplicate files with slightly different names (`UserService.js` and `userService_old.js`)
- [ ] Generated files committed to source control that should be in `.gitignore`

**Expected behavior:** Delete unused files — version control preserves history. Regularly audit for unreferenced files. Add generated files (`dist/`, `build/`, `*.generated.*`) to `.gitignore`.

---

## 11. Duplicate and Near-Duplicate Constants

### 11.1 Same Value, Different Names
- [ ] `MAX_RETRY = 3` in one file and `RETRY_LIMIT = 3` in another file
- [ ] `API_TIMEOUT = 5000` and `REQUEST_TIMEOUT = 5000` defined separately
- [ ] `STATUS_ACTIVE = 'active'` and `USER_STATUS_ACTIVE = 'active'` in different modules
- [ ] Same error message string defined multiple times with minor wording differences

**Expected behavior:** One name, one place for every constant. Cross-file constants belong in a shared `constants/` module. If two constants have different names but the same value and purpose, they are the same constant — consolidate them.

### 11.2 ENV Variables Read in Multiple Places
- [ ] `process.env.DATABASE_URL` read in 5 different files
- [ ] `os.environ['API_KEY']` scattered across multiple service files
- [ ] No central config module — each file reads env vars directly
- [ ] Same env var read with different fallback values in different files

**Expected behavior:** Read all environment variables once in a single config module (`config/index.js`, `config.py`, `application.properties`). Export typed, validated config values. Every other file imports from config — never reads `process.env` directly. This ensures: one place to add validation, one place to see all config, consistent fallback behavior.

### 11.3 Magic Numbers and Strings Repeated
- [ ] The number `86400` (seconds in a day) appearing in 3+ files without a named constant
- [ ] HTTP status codes written as raw numbers (`200`, `404`, `500`) throughout the codebase
- [ ] The same role string `'admin'` or `'superuser'` repeated in every authorization check
- [ ] Same regex pattern defined multiple times across different files

**Expected behavior:** Every magic number or string that appears more than once must be a named constant. Group related constants together: `HTTP_STATUS`, `USER_ROLES`, `TIME_CONSTANTS`. Import from the constants file everywhere.

---

## 12. Spaghetti Code Patterns

### 12.1 Mixed Abstraction Levels
- [ ] A function that mixes high-level orchestration with low-level implementation detail in the same body
- [ ] Calling `sendEmail()` (high level) and then manually constructing the SMTP socket (low level) in the same function
- [ ] Business rule logic mixed with raw SQL string construction in one function
- [ ] A React component that manages routing logic, API calls, form state, and renders JSX all at once

**Expected behavior:** Each function should operate at one level of abstraction. High-level functions call other functions — they don't implement detail. Low-level functions do one specific thing. If you read a function and some lines read like a story ("get user, validate order, process payment") while others read like implementation ("split string by comma, iterate array, check index"), the abstraction levels are mixed. Extract the implementation detail into named functions.

### 12.2 No Clear Entry Point or Flow
- [ ] Hard to determine where a feature starts executing
- [ ] Logic spread across 5+ files with no clear orchestration point
- [ ] Event handlers triggering other event handlers in a chain with no central coordinator
- [ ] Side effects triggered implicitly through module imports

**Expected behavior:** Every feature should have a clear entry point — one function or one module that orchestrates the flow. The flow should be readable top-to-bottom. Side effects should be explicit, not triggered by import order. A new developer should be able to find the entry point and trace the full flow without jumping across 10 unrelated files.

### 12.3 Tangled Control Flow
- [ ] A function with 3+ early returns caused by unrelated concerns mixed together
- [ ] `if/else` chains that handle both validation AND business logic AND formatting in the same block
- [ ] Exception handling used for normal control flow (throwing exceptions instead of returning values)
- [ ] Boolean variables set in one place and checked 50 lines later with no clear connection

**Expected behavior:** Separate concerns before combining them. Validate first (return early on invalid). Then execute business logic. Then format and return. Each phase in a named function. Use return values or result types for control flow — not exceptions. Keep the distance between a variable being set and being used as short as possible.

### 12.4 Function as a Dumping Ground
- [ ] A function that started small and grew to 200+ lines by adding edge cases over time
- [ ] A function with 15+ local variables
- [ ] A function that contains its own mini-algorithm that could be a separate named function
- [ ] A function where removing any one section would not affect the others (unrelated code co-located)

**Expected behavior:** Functions should be cohesive — every line should contribute to one clear purpose. If you can read a function and describe what it does without using the word "and", it has good cohesion. If the description is "it validates the input AND saves to the database AND sends a notification AND logs the action", it should be 4 functions.

### 12.5 Implicit Dependencies and Hidden State
- [ ] Functions relying on global variables being set before they are called
- [ ] Module-level state mutated from multiple places with no clear ownership
- [ ] Functions that work differently based on the order they are called
- [ ] Side effects not obvious from a function's signature (function named `getUser` that also increments a counter)

**Expected behavior:** Functions should declare their dependencies explicitly as parameters. Avoid module-level mutable state. If a function has side effects, name it accordingly (`fetchAndCacheUser` not `getUser`). Output should depend only on input — not on external state that may or may not be set.