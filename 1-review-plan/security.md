# Security Review Checklist

When reviewing security, check for every item in this list.
Follow OWASP Top 10 as the primary reference framework.
For each issue found, record: file path, line number, description, severity, OWASP category if applicable.

## Injection (OWASP A03)
- [ ] SQL queries built with string concatenation or interpolation using user input
- [ ] NoSQL queries constructed from unsanitized user input
- [ ] Shell commands built with user-controlled data
- [ ] LDAP queries constructed from user input
- [ ] Template injection via user-controlled template strings
- [ ] XML/HTML constructed from user input without encoding

**Severity guide:** Critical for direct injection, High for indirect/partial

## Authentication (OWASP A07)
- [ ] Passwords stored in plaintext or with weak hashing (MD5, SHA1)
- [ ] Missing password complexity enforcement
- [ ] JWT tokens with no expiry or excessively long expiry
- [ ] JWT tokens not validated on every request
- [ ] Session tokens not invalidated on logout
- [ ] No rate limiting on login endpoints
- [ ] Predictable or sequential session/token IDs
- [ ] Multi-factor authentication missing for sensitive operations

**Severity guide:** Critical for plaintext passwords, High for missing validation

## Authorization (OWASP A01)
- [ ] Missing authorization checks before accessing resources
- [ ] Authorization checks done on the frontend only
- [ ] IDOR: using user-supplied IDs to access resources without ownership check
- [ ] Privilege escalation: users able to access admin functions
- [ ] Horizontal privilege escalation: users accessing other users' data
- [ ] Missing role checks on sensitive API endpoints

**Severity guide:** Critical for auth bypass, High for IDOR

## Sensitive Data Exposure (OWASP A02)
- [ ] API keys, passwords, or secrets hardcoded in source code
- [ ] Secrets committed in config files that are checked into git
- [ ] PII (emails, names, addresses) written to logs
- [ ] Sensitive data returned in API responses unnecessarily
- [ ] Sensitive data stored unencrypted in database
- [ ] Passwords or tokens visible in URL parameters

**Severity guide:** Critical for hardcoded secrets, High for PII in logs

## Security Misconfiguration (OWASP A05)
- [ ] Debug mode enabled in production configuration
- [ ] Default credentials not changed
- [ ] Unnecessary features, ports, or services enabled
- [ ] Missing security headers (CORS, CSP, HSTS, X-Frame-Options)
- [ ] Error messages exposing stack traces or internal paths to users
- [ ] Directory listing enabled on file servers

**Severity guide:** High for debug in production, Medium for missing headers

## Vulnerable Dependencies (OWASP A06)
- [ ] Dependencies with known CVEs (CVSS ≥ 7.0 = High, ≥ 9.0 = Critical)
- [ ] Dependencies significantly out of date (2+ major versions behind)
- [ ] Dependencies with no active maintenance

**Severity guide:** Match CVSS score — ≥9.0 Critical, ≥7.0 High, ≥4.0 Medium

## Input Validation
- [ ] User input used without validation or sanitization
- [ ] File uploads with no type or size validation
- [ ] Missing length limits on string inputs
- [ ] No validation on numeric ranges
- [ ] Trusting client-supplied data for business logic decisions

**Severity guide:** High if input reaches database or file system

## Cryptography
- [ ] Weak algorithms: MD5, SHA1, DES, RC4 used for security purposes
- [ ] Hardcoded encryption keys or IVs
- [ ] Improper use of random (Math.random() for security tokens)
- [ ] Missing TLS/HTTPS enforcement
- [ ] Self-signed certificates in production

**Severity guide:** Critical for broken crypto in auth/data paths

## Cross-Site Scripting (OWASP A03)
- [ ] User input rendered in HTML without escaping
- [ ] innerHTML used with user-controlled content
- [ ] Missing Content-Security-Policy header
- [ ] DOM-based XSS via URL parameters rendered directly

**Severity guide:** High for stored XSS, Medium for reflected XSS
