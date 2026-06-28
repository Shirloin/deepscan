# Review Plan

This folder defines exactly what DeepScan looks for during every code review.
Claude reads the relevant files from this folder before analyzing any repository.

## Structure

Each review dimension has its own directory with:
- `_base.md` — universal checklist that applies to all tech stacks
- `<stack>.md` — stack-specific checklist that extends the base

## Dimensions

| Directory | Dimension | Base Checklist |
|---|---|---|
| `code-quality/` | Code Quality | DRY, SOLID, hardcoding, naming, complexity, file structure, spaghetti patterns |
| `security/` | Security | OWASP Top 10, injection, auth, access control, secrets, crypto |
| `architecture/` | Architecture | Separation of concerns, coupling, API design, patterns, observability |
| `performance/` | Performance | N+1, caching, memory, async patterns, frontend rendering |

## Supported Tech Stacks

| Stack | File | Type |
|---|---|---|
| React | `react.md` | Frontend |
| Angular | `angular.md` | Frontend |
| Next.js | `nextjs.md` | Full-stack |
| Vue | `vue.md` | Frontend |
| Node.js / Express | `nodejs-express.md` | Backend |
| NestJS | `nestjs.md` | Backend |
| Spring Boot | `spring-boot.md` | Backend |
| Django | `django.md` | Backend |
| FastAPI | `fastapi.md` | Backend |
| Laravel | `laravel.md` | Backend |
| Go / Gin | `go-gin.md` | Backend |
| Flask | `flask.md` | Backend |

## How Claude Uses These Files

1. Read all four `_base.md` files first
2. Detect the tech stack from the repository's dependency files
3. Read the matching `<stack>.md` file for each dimension
4. Apply BOTH base + stack-specific checklists during analysis
5. If multiple stacks are present, read all matching files

## Severity Definitions

| Severity | Definition |
|---|---|
| Critical | Exploitable now, data loss risk, auth bypass, secrets exposed |
| High | Significant security weakness or quality issue with real impact |
| Medium | Moderate concern, technical debt, maintainability risk |
| Low | Minor issue, style, small optimization |

## Risk Score Formula

Each dimension scored 0–25. Total 0–100. Lower is better.
Critical: +20pts · High: +10pts · Medium: +4pts · Low: +1pt
Cap per dimension: 25. Total cap: 100.
