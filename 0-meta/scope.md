# Scope

## Repositories in Scope

| Repo Abbreviation | Repository Name | GitHub URL | Stack | In Scope Since |
|---|---|---|---|---|
| US | user-service | https://github.com/your-org/user-service | Python, FastAPI, PostgreSQL | 2026-06-25 |
| FEB | frontend-easy-buy | https://github.com/your-org/frontend-easy-buy | React, TypeScript, Next.js | 2026-06-25 |
| BEB | backend-easy-buy | https://github.com/your-org/backend-easy-buy | Node.js, Express, MongoDB | 2026-06-25 |

> Add repositories here as they are onboarded to DeepScan.

## How to Add a Repository

1. Add it to the table above
2. Add its abbreviation to [finding-id-registry.md](finding-id-registry.md)
3. Create its folder in:
   - `1-analysis/repos/<repo-name>/`
   - `2-findings/repos/<repo-name>/`
   - `4-tickets/repos/<repo-name>/`
   - `6-trends/repos/<repo-name>/`
4. Add it to [7-automation/scan-config.md](../7-automation/scan-config.md)

## What Is Out of Scope

- Third-party vendor code
- Auto-generated files (e.g., migration files, compiled output)
- Documentation-only repositories
- Archived / deprecated repositories

## Exclusion Rules

Files and paths excluded from analysis:

```
node_modules/
dist/
build/
*.min.js
*.generated.*
migrations/
__pycache__/
.env*
```
