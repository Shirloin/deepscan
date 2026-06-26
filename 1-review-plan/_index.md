# Review Plan

This folder defines exactly what DeepScan looks for during a code review.
Claude reads these files before analyzing any repository.

There are four review dimensions. Each has its own checklist file.

| File | Dimension | Focus |
|---|---|---|
| [code-quality.md](code-quality.md) | Code Quality | Maintainability, readability, correctness |
| [security.md](security.md) | Security | Vulnerabilities, OWASP Top 10, secrets, auth |
| [architecture.md](architecture.md) | Architecture | Structure, coupling, separation of concerns |
| [performance.md](performance.md) | Performance | Queries, caching, complexity, memory |

## Severity Definitions

| Severity | Definition | Ticket Rule |
|---|---|---|
| **Critical** | Exploitable now, data loss risk, auth bypass, secrets exposed | Ticket immediately |
| **High** | Significant security weakness or quality issue with real impact | Ticket immediately |
| **Medium** | Moderate concern, technical debt, maintainability risk | Ticket after 2 consecutive runs |
| **Low** | Minor issue, style, small optimization | Log only, no ticket |

## Risk Score Calculation

Each dimension is scored out of 25. Total is out of 100. Lower is better.

| Finding Severity | Score Impact |
|---|---|
| Critical | +20 per finding |
| High | +10 per finding |
| Medium | +4 per finding |
| Low | +1 per finding |

Cap per dimension: 25. Total cap: 100.
