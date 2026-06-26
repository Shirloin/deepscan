# Review Plan

This folder defines exactly what DeepScan looks for during every code review.
Claude reads ALL files in this folder before analyzing any repository.

There are four review dimensions. Each has a dedicated checklist file.

| File | Dimension | Primary Focus |
|---|---|---|
| [code-quality.md](code-quality.md) | Code Quality | DRY, SOLID, hardcoding, naming, complexity, error handling, hygiene |
| [security.md](security.md) | Security | OWASP Top 10, injection, auth, access control, secrets, crypto |
| [architecture.md](architecture.md) | Architecture | Separation of concerns, coupling, API design, patterns, observability |
| [performance.md](performance.md) | Performance | N+1 queries, caching, memory leaks, async patterns, frontend rendering |

---

## Coverage

All checklists cover both **frontend** and **backend** codebases:
- Frontend: React, Vue, Angular, Next.js, and equivalents
- Backend: Node.js, Python, Java, Go, PHP, and equivalents
- Full-stack: Next.js, Nuxt, SvelteKit, and equivalents

---

## How to Apply These Checklists

1. Read all four checklist files completely before starting analysis
2. For each file in the repository, apply all relevant checklist items
3. For each violation found, record:
   - **Finding ID** — derived from `2-config/finding-id-registry.md`
   - **File path** and **line number**
   - **What the violation is** — specific, not generic
   - **Dimension** — which of the four categories
   - **Severity** — see definitions below
   - **Expected behavior** — include the solution suggestion from the checklist

---

## Severity Definitions

| Severity | Definition | Examples |
|---|---|---|
| **Critical** | Exploitable now, data loss risk, auth bypass, secrets exposed in code | SQL injection, hardcoded API keys, auth bypass, plaintext passwords |
| **High** | Significant security weakness or quality issue with real impact | Missing auth checks, N+1 in hot path, SOLID violations making code untestable, missing error handling on critical paths |
| **Medium** | Moderate concern — technical debt, maintainability risk, potential future issue | Code duplication, missing caching, inconsistent patterns, missing pagination |
| **Low** | Minor issue — style, small optimization, hygiene | Debug logs left in, dead code, inconsistent naming, minor complexity |

---

## Auto-Confirmed Findings (no manual verification needed)

These finding types are confirmed immediately:
- Hardcoded credentials, API keys, or secrets in source code → **Critical**
- SQL/NoSQL injection via direct string concatenation with user input → **Critical**
- Passwords stored with MD5 or SHA1 → **Critical**
- Debug mode confirmed enabled in production config → **High**
- Packages with known CVEs at CVSS ≥ 4.0 → match CVE severity

---

## Risk Score Calculation

Each dimension scored out of 25. Total out of 100. **Lower is better.**

| Finding Severity | Score Impact Per Finding |
|---|---|
| Critical | +20 points |
| High | +10 points |
| Medium | +4 points |
| Low | +1 point |

Cap per dimension: 25 points maximum.
Total cap: 100 points maximum.

| Score Range | Meaning |
|---|---|
| 0–20 | Excellent — minimal issues |
| 21–40 | Good — minor issues present |
| 41–60 | Moderate — attention needed |
| 61–80 | Poor — significant issues |
| 81–100 | Critical — immediate action required |

---

## Finding Reporting Format

For every finding, use this format when writing to `repos/<repo-name>/findings.md`:

```
ID:        <ABBR>-<SEVERITY><n>
Severity:  Critical | High | Medium | Low
Dimension: Code Quality | Security | Architecture | Performance
File:      <relative file path from repo root>
Line:      <line number or range>
Title:     <short, specific title — not generic>
           Bad:  "SQL Injection risk"
           Good: "SQL injection in authenticateUser() via string concatenation"

Description:
  What the violation is, specifically. Reference the function name,
  variable name, or pattern involved.

Expected Behavior (with solution):
  What should happen instead. Include the specific fix approach.
  Reference the checklist item that was violated.
```