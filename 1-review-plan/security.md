# Security Review Checklist

When reviewing security, check every section in this file against the codebase.
Applies to all tech stacks: React, Vue, Angular, Next.js, Node.js, Python, Java, Go, PHP, and others.
Primary reference: OWASP Top 10 (2021).

For each violation found, record:
- **File** and **line number**
- **What the violation is**
- **Expected behavior** including a solution suggestion

---

## 1. Injection (OWASP A03)

### 1.1 SQL Injection
- [ ] User input concatenated into SQL query strings
- [ ] String interpolation used to build SQL queries
- [ ] Raw SQL queries with unsanitized parameters
- [ ] ORM raw query methods called with user input directly (`query()`, `raw()`, `execute()`)

**Severity:** Critical — auto-confirmed
**Expected behavior:** Always use parameterized queries or prepared statements. With ORMs, use the query builder methods that handle parameterization automatically. Never concatenate user input into SQL strings.
```
// Bad:  "SELECT * FROM users WHERE email = '" + email + "'"
// Good: "SELECT * FROM users WHERE email = ?", [email]
```

### 1.2 NoSQL Injection
- [ ] MongoDB queries built with unsanitized user input objects
- [ ] User-controlled keys used in query operators (`$where`, `$gt`, `$regex`)
- [ ] JSON.parse on user input used directly as a query filter

**Severity:** Critical
**Expected behavior:** Validate and sanitize all query parameters. Use schema validation (Joi, Zod, Mongoose schema) to enforce expected types. Never allow user input to control query operators.

### 1.3 Command Injection
- [ ] Shell commands built with user-supplied input (`exec`, `spawn`, `system`, `subprocess`)
- [ ] File paths constructed from user input passed to shell commands
- [ ] Template strings used to build CLI commands

**Severity:** Critical
**Expected behavior:** Never build shell commands from user input. Use language-native libraries for file operations instead of shell commands. If shell execution is unavoidable, use argument arrays (not string concatenation) and validate/whitelist input strictly.

### 1.4 LDAP Injection
- [ ] LDAP queries constructed with unsanitized user input
- [ ] User-supplied DN components not escaped

**Severity:** High
**Expected behavior:** Escape all special LDAP characters in user-supplied input. Use parameterized LDAP APIs where available.

### 1.5 Template Injection (SSTI)
- [ ] User input rendered inside server-side templates (Jinja2, Twig, Handlebars, etc.)
- [ ] User-controlled template strings evaluated server-side
- [ ] `eval()` or `Function()` called with user-supplied strings

**Severity:** Critical
**Expected behavior:** Never pass user input as a template string. Use safe rendering methods that treat user data as values, not template code. Disable dangerous template features (Jinja2 sandbox, etc.).

---

## 2. Broken Authentication (OWASP A07)

### 2.1 Password Storage
- [ ] Passwords stored in plaintext
- [ ] Passwords hashed with MD5 or SHA1 (not password hashing algorithms)
- [ ] Passwords hashed without salt
- [ ] Using SHA256/SHA512 directly instead of bcrypt, Argon2, or scrypt

**Severity:** Critical — auto-confirmed
**Expected behavior:** Use a dedicated password hashing algorithm: bcrypt (cost factor ≥ 12), Argon2id, or scrypt. Never use general-purpose hash functions for passwords. Always use a unique salt per password (built into bcrypt/Argon2).

### 2.2 Token Security
- [ ] JWT tokens with no expiry (`exp` claim missing)
- [ ] JWT tokens with excessively long expiry (> 24 hours for access tokens)
- [ ] JWT signed with `alg: none`
- [ ] JWT secret stored in code or committed to git
- [ ] Session tokens not invalidated on logout
- [ ] Refresh tokens stored in localStorage (XSS vulnerable)
- [ ] Predictable or sequential token/session ID generation

**Severity:** High to Critical
**Expected behavior:** Access tokens: short expiry (15–60 minutes). Refresh tokens: longer expiry, stored in httpOnly cookies. JWT secret from environment variables, not code. On logout, invalidate the session server-side. Use cryptographically random token generation.

### 2.3 Authentication Gaps
- [ ] No rate limiting on login endpoints (brute force possible)
- [ ] No account lockout after repeated failed attempts
- [ ] Login endpoint revealing whether email exists vs wrong password
- [ ] Password reset tokens not expiring or single-use
- [ ] No MFA for sensitive operations (admin access, payment changes, etc.)
- [ ] Remember-me tokens stored insecurely

**Severity:** High
**Expected behavior:** Rate limit authentication endpoints (e.g., 5 attempts per 15 minutes per IP). Return generic messages on auth failure. Password reset tokens must be single-use and expire within 1 hour. Implement MFA for privileged operations.

---

## 3. Broken Access Control (OWASP A01)

### 3.1 Missing Authorization Checks
- [ ] API endpoints that perform actions without checking if the caller is authorized
- [ ] Authorization checks only on the frontend — not enforced server-side
- [ ] Admin endpoints accessible without admin role check
- [ ] Sensitive data returned based on authentication alone, without authorization

**Severity:** Critical
**Expected behavior:** Every endpoint that accesses or modifies data must verify authorization server-side. Never trust frontend-enforced access control. Apply a middleware-based auth check at the route level.

### 3.2 Insecure Direct Object Reference (IDOR)
- [ ] Using user-supplied IDs (from URL params, body, query string) to access records without ownership check
- [ ] `GET /api/orders/1234` — no check that order 1234 belongs to the requesting user
- [ ] File downloads by filename without path traversal protection

**Severity:** Critical
**Expected behavior:** Always check that the authenticated user owns or has permission to access the requested resource. `WHERE id = ? AND user_id = current_user_id`. Never expose sequential integer IDs for sensitive resources — use UUIDs.

### 3.3 Privilege Escalation
- [ ] Users able to modify their own role or permission level via API
- [ ] Admin functionality exposed on the same route with a query param (`?admin=true`)
- [ ] Role or permission data taken from the request body instead of the authenticated session

**Severity:** Critical
**Expected behavior:** Role and permission data must come exclusively from the server-side session or JWT claims — never from user-supplied input. Role changes must require a separate authorized flow.

### 3.4 Mass Assignment
- [ ] ORM model accepting all request body fields without allowlist (`User.create(req.body)`)
- [ ] No field filtering on update operations
- [ ] Users able to set fields like `isAdmin`, `role`, `balance` by including them in a request

**Severity:** High
**Expected behavior:** Explicitly allowlist the fields that can be set from user input. Use DTO/schema validation to strip unexpected fields before passing to the model.

---

## 4. Sensitive Data Exposure (OWASP A02)

### 4.1 Secrets in Code
- [ ] API keys hardcoded in source files
- [ ] Database passwords in connection strings in code
- [ ] Private keys or certificates committed to the repository
- [ ] Secrets in comments or documentation committed to git

**Severity:** Critical — auto-confirmed
**Expected behavior:** All secrets via environment variables or secrets manager. Audit git history if a secret was ever committed — rotate the secret immediately, it is compromised.

### 4.2 PII and Sensitive Data in Logs
- [ ] Passwords logged (even as "received password: [hidden]" is risky)
- [ ] Email addresses, names, or phone numbers in log output
- [ ] Payment card numbers or bank account numbers logged
- [ ] Full request bodies logged without sanitization
- [ ] JWTs or session tokens appearing in logs

**Severity:** High
**Expected behavior:** Never log credentials, tokens, or PII. Implement a log sanitizer that strips sensitive fields from request logs. Use structured logging with explicit field allowlists.

### 4.3 Sensitive Data in API Responses
- [ ] Password hash returned in user API responses
- [ ] Internal system fields (created_by_admin, internal_flags) returned to regular users
- [ ] Full payment card data returned when only last 4 digits are needed
- [ ] Sensitive fields returned in list endpoints that only need summary data

**Expected behavior:** Use response serializers or DTOs that explicitly define which fields are returned. Never pass raw database models directly to API responses. Strip sensitive fields at the serialization layer.

### 4.4 Insecure Data Transmission
- [ ] HTTP endpoints used instead of HTTPS for anything carrying user data
- [ ] No HSTS header to enforce HTTPS
- [ ] Sensitive data passed in URL query parameters (logged by servers and proxies)

**Expected behavior:** Enforce HTTPS everywhere. Add `Strict-Transport-Security` header. Never put sensitive data in URLs — use request body or headers.

---

## 5. Security Misconfiguration (OWASP A05)

### 5.1 Debug and Development Settings in Production
- [ ] Debug mode enabled in production (`DEBUG=True`, `NODE_ENV=development`)
- [ ] Verbose error messages or stack traces returned to API consumers
- [ ] Development-only middleware enabled in production (e.g., detailed error pages)
- [ ] Default admin credentials not changed

**Severity:** High — auto-confirmed for debug mode
**Expected behavior:** Use environment-specific configuration. Production must have debug disabled, generic error messages for consumers, and structured error logging server-side only.

### 5.2 Missing Security Headers
- [ ] No `Content-Security-Policy` header
- [ ] No `X-Content-Type-Options: nosniff`
- [ ] No `X-Frame-Options` or `frame-ancestors` CSP directive
- [ ] No `Referrer-Policy`
- [ ] CORS configured with `*` (wildcard) on authenticated APIs
- [ ] CORS `Access-Control-Allow-Origin` set to user-supplied `Origin` header without validation

**Severity:** Medium to High
**Expected behavior:** Apply security headers via middleware (Helmet.js for Node, SecurityFilterChain for Spring, django-csp for Django). CORS must specify exact allowed origins for authenticated endpoints — never wildcard.

### 5.3 Unnecessary Exposure
- [ ] `.git` folder accessible on the web server
- [ ] `package.json`, `.env`, or config files served as static files
- [ ] Admin or debug endpoints exposed publicly without auth
- [ ] Server version information exposed in response headers (`X-Powered-By`, `Server`)

**Expected behavior:** Disable directory listing. Remove or restrict access to sensitive files. Remove server identification headers. Audit publicly accessible paths.

---

## 6. Vulnerable and Outdated Dependencies (OWASP A06)

### 6.1 Known CVEs
- [ ] Dependencies with CVEs at CVSS ≥ 4.0
- [ ] Dependencies flagged by `npm audit`, `pip audit`, `snyk`, `OWASP Dependency-Check`
- [ ] Transitive (indirect) dependencies with critical CVEs

**Severity:** Match CVSS — ≥ 9.0 Critical, ≥ 7.0 High, ≥ 4.0 Medium
**Expected behavior:** Run dependency audit as part of CI. Upgrade or replace vulnerable packages. For packages that cannot be upgraded, document the accepted risk and mitigating controls.

### 6.2 Significantly Outdated Dependencies
- [ ] Major versions significantly behind the current release (2+ major versions)
- [ ] End-of-life runtime versions (Node.js EOL, Python 2, Java 8 EOL, etc.)
- [ ] Packages with no maintenance activity in 2+ years used in security-sensitive paths

**Expected behavior:** Maintain a dependency update schedule. Use tools like Dependabot or Renovate for automated PR creation on updates. Track EOL dates for runtimes and frameworks.

---

## 7. Cross-Site Scripting (OWASP A03)

### 7.1 Stored and Reflected XSS
- [ ] User input rendered in HTML without encoding/escaping
- [ ] `innerHTML`, `document.write()`, `outerHTML` set with user-controlled content
- [ ] React `dangerouslySetInnerHTML` used with user data
- [ ] Vue `v-html` used with user data
- [ ] Server-side templates rendering user input without auto-escaping

**Severity:** High for stored XSS, Medium for reflected
**Expected behavior:** Never use `innerHTML` or equivalent with user data. Use safe DOM APIs (`textContent`, `createElement`). In templates, use the framework's built-in escaping (React JSX escapes by default, Jinja2 auto-escaping). If rich HTML input is needed, use a strict sanitization library (DOMPurify).

### 7.2 DOM-Based XSS
- [ ] `location.hash`, `location.search`, `document.referrer` written to DOM without sanitization
- [ ] URL parameters rendered directly into the page
- [ ] `eval()` or `setTimeout(string)` called with user-controlled strings

**Expected behavior:** Treat all URL-sourced data as untrusted. Never use `eval()`. Use `JSON.parse` with try/catch instead of `eval` for JSON. Sanitize before any DOM insertion.

---

## 8. Cryptography

### 8.1 Weak Algorithms
- [ ] MD5 used for any security purpose (even checksums in security contexts)
- [ ] SHA1 used for password hashing or token signing
- [ ] DES or 3DES used for encryption
- [ ] RC4 used for any purpose
- [ ] Custom/homebrew encryption implementation

**Severity:** High to Critical
**Expected behavior:** Use modern algorithms: AES-256-GCM for symmetric encryption, RSA-2048+ or EC P-256+ for asymmetric, SHA-256+ for hashing, bcrypt/Argon2 for passwords. Never implement your own cryptography.

### 8.2 Improper Key and IV Management
- [ ] Hardcoded encryption keys in code
- [ ] Static (non-random) initialization vectors (IVs) for AES
- [ ] Reused nonces in AES-GCM
- [ ] Encryption keys stored in the same location as encrypted data

**Severity:** High
**Expected behavior:** Generate IVs randomly for every encryption operation. Store keys in a secrets manager separate from the application. Rotate keys periodically.

### 8.3 Insecure Randomness
- [ ] `Math.random()` used to generate security tokens, session IDs, or OTPs
- [ ] `random.random()` (Python) used for security-sensitive values
- [ ] Predictable seeds used for random number generation

**Severity:** High
**Expected behavior:** Use cryptographically secure random generators: `crypto.randomBytes()` (Node.js), `secrets` module (Python), `SecureRandom` (Java), `crypto/rand` (Go).

---

## 9. Input Validation and Sanitization

### 9.1 Missing Validation
- [ ] Request body, query params, or headers used without schema validation
- [ ] File uploads with no type validation (checking MIME type AND file content, not just extension)
- [ ] File uploads with no size limit
- [ ] No length limits on string inputs (can cause DoS or database truncation issues)
- [ ] Numeric inputs not validated for range or type

**Severity:** High when input reaches database, file system, or shell
**Expected behavior:** Validate ALL input at the entry point. Use schema validation libraries (Zod, Joi, Pydantic, Marshmallow, Bean Validation). Reject and return 400 for invalid input before any processing.

### 9.2 Path Traversal
- [ ] User-supplied filenames used to construct file paths without sanitization
- [ ] `../` sequences not stripped from user-supplied paths
- [ ] File operations on paths that aren't verified to be within the expected directory

**Severity:** High
**Expected behavior:** Use `path.resolve()` and verify the result starts with the expected base directory. Use a whitelist of allowed filenames or generate server-side filenames. Never use user input directly as a file path.

---

## 10. Frontend-Specific Security

### 10.1 Sensitive Data in Frontend
- [ ] API keys or secrets included in frontend bundle
- [ ] JWT stored in localStorage (accessible to XSS)
- [ ] Sensitive user data cached in localStorage or sessionStorage without encryption
- [ ] Business logic that should be server-side implemented only in frontend

**Severity:** High
**Expected behavior:** No secrets in frontend code — all sensitive operations via backend API. Store JWTs in httpOnly cookies. Treat all frontend code as public — anyone can read it.

### 10.2 Security Through Obscurity
- [ ] Access control enforced by hiding UI elements without server-side enforcement
- [ ] Admin routes only hidden from navigation but accessible directly via URL
- [ ] Feature flags checked only on the frontend

**Expected behavior:** All access control must be enforced server-side. Frontend hiding of UI is UX only — never security. Assume users can and will access any URL or API endpoint directly.

---

## 11. Cross-File Security Patterns (requires full repository read)

### 11.1 Inconsistent Auth Enforcement
- [ ] Some routes have auth middleware, others don't — with no clear rule about which should
- [ ] Auth check implemented differently in different parts of the codebase (JWT in one place, session in another)
- [ ] Some controllers check permissions, others assume the middleware already did it
- [ ] Middleware applied inconsistently across route groups

**Expected behavior:** Auth enforcement must be consistent and centralized. Use middleware applied at the router level. Every route that requires auth should be under an auth-protected router group. Audit the full route list to confirm no endpoint is accidentally unprotected.

### 11.2 Secret Patterns Across Files
- [ ] Same secret or credential used in multiple files (copied, not imported from one source)
- [ ] Different files using different methods to access the same secret (`process.env.KEY` in some, hardcoded in others)
- [ ] Secrets passed as function parameters through multiple call layers instead of read from config once

**Expected behavior:** Every secret read once in the config module. Never pass secrets as function parameters — inject via config or dependency injection. Audit all files for string patterns that look like secrets.

### 11.3 Input Validation Coverage Gaps
- [ ] Validation applied on some endpoints but missing on endpoints added later
- [ ] Consistent validation on POST/PUT endpoints but missing on DELETE or GET with query params
- [ ] Validation in the controller but missing in internal service methods that are also called directly

**Expected behavior:** Validate at every entry point — HTTP endpoints, message queue consumers, cron job inputs, CLI arguments. Internal service methods that can be called from multiple places should also validate their inputs. Audit all entry points across the full codebase.