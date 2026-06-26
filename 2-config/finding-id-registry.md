# Finding ID Registry

The ONLY file Claude writes to outside of repos/.
Update the counters after every run. Never reuse a retired ID.

## Repository Counters

| Abbreviation | Repository Name | Next Critical | Next High | Next Medium | Next Low |
|---|---|---|---|---|---|
| BEB | backend-easy-buy | C02 | H05 | M09 | L03 |
| FEB | frontend-easy-buy | C01 | H01 | M06 | L03 |

> Add one row per repository. Derive abbreviation from repo name:
> Split on `-` and `_`, take first letter of each word, uppercase.
> Examples: `user-service` → `US`, `frontend-easy-buy` → `FEB`

## All Finding IDs Ever Assigned

| Finding ID | Repository | Severity | Summary | First Seen | Status |
|---|---|---|---|---|---|
| BEB-C01 | backend-easy-buy | Critical | Predictable hardcoded fallback JWT/session secret | 2026-06-26 | Active (ticketed #9) |
| BEB-H01 | backend-easy-buy | High | Broken access control (IDOR) on product update/delete | 2026-06-26 | Active (ticketed #10) |
| BEB-H02 | backend-easy-buy | High | Login/validate-token expose bcrypt password hash | 2026-06-26 | Active (ticketed #11) |
| BEB-H03 | backend-easy-buy | High | `.env.local` with SECRET_KEY committed to repo | 2026-06-26 | Active (ticketed #12) |
| BEB-H04 | backend-easy-buy | High | authMiddleware hangs when Authorization header absent | 2026-06-26 | Active (ticketed #13) |
| BEB-M01 | backend-easy-buy | Medium | No rate limiting on /login | 2026-06-26 | Active (ticketed) |
| BEB-M02 | backend-easy-buy | Medium | User-controlled RegExp in product search (ReDoS) | 2026-06-26 | Active (ticketed) |
| BEB-M03 | backend-easy-buy | Medium | CORS origin `*` with credentials true | 2026-06-26 | Active (ticketed) |
| BEB-M04 | backend-easy-buy | Medium | `deleteProduct` repository call not awaited | 2026-06-26 | Active (ticketed) |
| BEB-M05 | backend-easy-buy | Medium | DB connection URL leaked via logs | 2026-06-26 | Active (ticketed) |
| BEB-M06 | backend-easy-buy | Medium | logout does not destroy session / invalidate token | 2026-06-26 | Active (ticketed) |
| BEB-M07 | backend-easy-buy | Medium | No automated tests | 2026-06-26 | Active (ticketed) |
| BEB-M08 | backend-easy-buy | Medium | N+1 sequential writes in createTransaction | 2026-06-26 | Active (ticketed) |
| BEB-L01 | backend-easy-buy | Low | 50mb request body limit | 2026-06-26 | Active (ticketed) |
| BEB-L02 | backend-easy-buy | Low | createTransaction guard-order bug | 2026-06-26 | Active (ticketed) |
| FEB-M01 | frontend-easy-buy | Medium | JWT stored in localStorage (XSS token theft) | 2026-06-26 | Active (ticketed) |
| FEB-M02 | frontend-easy-buy | Medium | Auth header placed in request body, not sent (ProductService) | 2026-06-26 | Active (ticketed) |
| FEB-M03 | frontend-easy-buy | Medium | No centralized axios client / inconsistent auth attachment | 2026-06-26 | Active (ticketed) |
| FEB-M04 | frontend-easy-buy | Medium | ProtectedRoute navigates during render | 2026-06-26 | Active (ticketed) |
| FEB-M05 | frontend-easy-buy | Medium | No automated tests | 2026-06-26 | Active (ticketed) |
| FEB-L01 | frontend-easy-buy | Low | Production source maps enabled (build.sourcemap) | 2026-06-26 | Active (ticketed) |
| FEB-L02 | frontend-easy-buy | Low | Unused `dotenv` dependency in Vite app | 2026-06-26 | Active (ticketed) |

## Fingerprint Format

Each finding is fingerprinted as:
`<repo-abbreviation>:<file-path>:<issue-category>:<issue-type>`

Same fingerprint in a new run = recurring finding. Update `last seen` date only.
Do NOT assign a new ID to a recurring finding.
