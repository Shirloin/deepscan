# Code Quality Review Checklist

When reviewing code quality, check for every item in this list.
For each issue found, record: file path, line number, description, severity.

## Dead Code
- [ ] Unused variables declared but never referenced
- [ ] Unused function parameters
- [ ] Unused imports or dependencies imported but never used
- [ ] Functions defined but never called
- [ ] Commented-out code blocks left in production files
- [ ] Unreachable code after return/throw statements

**Severity guide:** Low unless scale is large (10+ instances = Medium)

## Code Duplication
- [ ] Identical or near-identical logic blocks in multiple files
- [ ] Copy-pasted functions with minor variable name changes
- [ ] Repeated conditional logic that could be abstracted
- [ ] Same constants defined in multiple places

**Severity guide:** Medium if duplication spans 3+ files or 20+ lines

## Function and Class Design
- [ ] Functions longer than 50 lines doing multiple things
- [ ] Functions with more than 4–5 parameters
- [ ] God classes/modules that own too much logic
- [ ] Deeply nested code (4+ levels of indentation)
- [ ] Circular dependencies between modules

**Severity guide:** Medium for functions >100 lines, High if it obscures critical logic

## Naming and Readability
- [ ] Single-letter variable names outside of loop counters
- [ ] Misleading names (function named `getUser` that also writes to DB)
- [ ] Inconsistent naming conventions across the codebase
- [ ] Magic numbers or strings without named constants

**Severity guide:** Low for individual cases, Medium if systemic

## Error Handling
- [ ] Empty catch blocks that swallow errors silently
- [ ] Missing error handling on async operations
- [ ] Generic error messages with no actionable context
- [ ] Errors logged but not propagated when they should be
- [ ] Missing null/undefined checks before property access

**Severity guide:** High if in critical paths (auth, payments, data writes)

## Test Coverage
- [ ] Critical business logic with no tests
- [ ] Authentication or authorization logic with no tests
- [ ] Payment or financial logic with no tests
- [ ] Public API endpoints with no tests

**Severity guide:** High for untested auth/payment logic, Medium for other critical paths

## Code Hygiene
- [ ] Console.log or debug print statements left in production code
- [ ] TODO/FIXME comments older than 30 days in critical files
- [ ] Hardcoded values that should come from config or environment
- [ ] Inconsistent formatting that indicates lack of linting

**Severity guide:** Low individually, Medium if systemic
