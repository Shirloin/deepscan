# CLAUDE.md — DeepScan

Read this file completely before doing anything in this project.

---

## What This Project Is

DeepScan is a manual AI-powered code review system.
It reviews GitHub repositories for code quality, security, architecture,
and performance issues — then creates GitHub Issues for ALL findings
and adds them to a shared GitHub Project backlog.

---

## Two Layers — Read vs Write

**You READ from (never write to):**
- `CLAUDE.md` (this file)
- `deepscan.config.json`
- `0-meta/`
- `1-review-plan/`
- `2-config/ticket-rules.md`
- `2-config/verification-rules.md`
- `2-config/excluded-paths.md`

**You WRITE to (results only):**
- `2-config/finding-id-registry.md` — update ID counters only
- `repos/_index.md` — update master table
- `repos/<repo-name>/analysis.md` — analysis output
- `repos/<repo-name>/findings.md` — finding records
- `repos/<repo-name>/verification.md` — verification status
- `repos/<repo-name>/tickets.md` — ticket log
- `repos/<repo-name>/plan.md` — fix priorities
- `repos/<repo-name>/delta.md` — run delta

**Never modify any file in `0-meta/`, `1-review-plan/`, or `2-config/`
except for the ID counter updates in `finding-id-registry.md`.**

---

## Execution Order

Follow this sequence top to bottom on every run. Do not skip steps.

```
── STEP 1: READ CONFIG ────────────────────────────────────────────────
  deepscan.config.json
    → which repos to review
    → github_project details (project_id, project_number, default_column)
    → global_excluded_paths

── STEP 2: READ RULES (before touching any code) ─────────────────────
  1-review-plan/_index.md           → severity definitions, score formula
  1-review-plan/code-quality.md     → code quality checklist
  1-review-plan/security.md         → security checklist
  1-review-plan/architecture.md     → architecture checklist
  1-review-plan/performance.md      → performance checklist
  2-config/finding-id-registry.md   → current ID counters for this repo
  2-config/ticket-rules.md          → duplicate check, issue format, labels
  2-config/verification-rules.md    → what confirms or refutes a finding
  2-config/excluded-paths.md        → paths to skip during analysis

── STEP 3: READ EXISTING STATE (before analyzing) ────────────────────
  repos/<repo-name>/findings.md     → existing findings and their status
  repos/<repo-name>/tickets.md      → existing tickets (open, closed, skipped)
  repos/<repo-name>/analysis.md     → previous run scores for delta comparison

  Purpose: understand what already exists before the review starts.
  This prevents duplicate findings and duplicate tickets.

── STEP 4: READ THE FULL REPOSITORY (GitHub MCP — read only) ─────────

  PHASE 1 — MAP THE ENTIRE REPOSITORY (do this before reading any file)

  1. Get the complete recursive file tree of the entire repository
     Use GitHub MCP to list ALL files and directories from the root
     Do not sample — get every file path

  2. Filter out excluded paths from:
     - 2-config/excluded-paths.md
     - global_excluded_paths in deepscan.config.json

  3. What remains is your FULL READ LIST — every source file must be read

  PHASE 2 — READ EVERY FILE IN THE FULL READ LIST

  Read files in this order (most likely to contain findings first):
    a) Entry points — main files, app bootstraps, index files, server files
    b) Route and controller files — all of them
    c) Service and business logic files — all of them
    d) Data access and repository files — all of them
    e) Auth and middleware files — all of them
    f) Configuration files — all of them
    g) Utility and helper files — all of them
    h) Component files (frontend) — all of them
    i) Hook and store files (frontend) — all of them
    j) Test files — all of them (to understand coverage gaps)
    k) Remaining source files — all of them

  CRITICAL RULES FOR READING:
  - Read EVERY file in the full read list — no file limit, no sampling
  - Do not stop reading when you think you have "enough context"
  - Do not skip files because they seem unimportant
  - Cross-file issues (duplicate constants, inconsistent patterns,
    spaghetti dependencies) can ONLY be found by reading every file
  - If a repository has 200 files, read all 200

  WHAT TO BUILD WHILE READING:
  While reading, maintain a running mental model of:
  - Directory structure and whether it follows a clear pattern
  - File naming conventions and whether they are consistent
  - Constants and values defined — watch for duplicates across files
  - Which patterns are used (are they consistent across the codebase?)
  - Dependencies between files — who imports what
  - Any value, string, or logic that appears in more than one file

  PHASE 3 — APPLY ALL CHECKLISTS AGAINST THE FULL PICTURE

  Only after reading EVERY file, apply all checklists from 1-review-plan/:
  - 1-review-plan/code-quality.md
  - 1-review-plan/security.md
  - 1-review-plan/architecture.md
  - 1-review-plan/performance.md

  Pay special attention to cross-file sections in each checklist:
  - Duplicate constants across files (Section 11 in code-quality.md)
  - Spaghetti patterns (Section 12 in code-quality.md)
  - File and directory structure (Section 10 in code-quality.md)
  - Inconsistent patterns across the codebase (Section 8 in architecture.md)
  - Cross-file security gaps (Section 11 in security.md)
  - Cross-file performance patterns (Section 8 in performance.md)

  Build finding candidates: file, line, dimension, issue type, severity

── STEP 5: RECONCILE FINDINGS ────────────────────────────────────────
  For each finding candidate:

  a) Generate fingerprint: <repo>:<file-path>:<dimension>:<issue-type>

  b) Check finding-id-registry.md for existing fingerprint:
     → Match found = RECURRING finding
       - Do NOT assign a new ID
       - Update "Last Seen" date in findings.md
       - Note in delta.md as recurring
     → No match = NEW finding
       - Assign next available ID from registry counters
       - Add to findings.md as Active
       - Add fingerprint + ID to finding-id-registry.md
       - Note in delta.md as new

── STEP 6: VERIFY FINDINGS ───────────────────────────────────────────
  Apply 2-config/verification-rules.md to each finding:

  Auto-confirmed (no queue needed):
  - Hardcoded credentials or secrets
  - Direct SQL/NoSQL injection via string concatenation
  - Packages with known CVEs (CVSS ≥ 4.0)
  - Debug mode in production config files

  All others:
  - Confirm if all verification criteria are met
  - Refute if any refutation criteria are met
  - Update repos/<repo-name>/verification.md

── STEP 7: WRITE ANALYSIS ────────────────────────────────────────────
  repos/<repo-name>/analysis.md
    → risk score breakdown per dimension
    → observations per dimension
    → raw candidates table

── STEP 8: WRITE FINDINGS ────────────────────────────────────────────
  repos/<repo-name>/findings.md
    → add new findings to Active table
    → update Last Seen for recurring findings
    → add detail blocks for Critical and High findings

── STEP 9: DUPLICATE CHECK + TICKET CREATION (GitHub MCP — write) ────
  For EVERY confirmed finding, run the full duplicate check sequence
  defined in 2-config/ticket-rules.md BEFORE creating any issue.

  Duplicate check sequence (mandatory, in this order):

  CHECK 1 — Local findings.md
    Does this finding ID already have a GitHub Issue number?
    YES → go to Merge/Update Logic in ticket-rules.md
    NO  → continue to Check 2

  CHECK 2 — Local tickets.md
    Is this finding ID in Open, Closed, or Skipped tables?
    Open   → go to Merge/Update Logic
    Closed → go to Reopen Logic
    Skipped → go to Skip Re-evaluation Logic
    Not found → continue to Check 3

  CHECK 3 — GitHub Issues via MCP (source of truth)
    Search repo issues for "[<FINDING-ID>]" in title
    Also search by finding title keywords
    Open match   → go to Merge/Update Logic
    Closed match → go to Reopen Logic
    Manual issue same topic → go to Adopt Logic
    No match → CREATE new issue

  After all 3 checks pass with no match:
  → Create GitHub Issue using format in ticket-rules.md
  → Apply all 3 label types: system + priority + category
  → Add to GitHub Project board (project_id from config)
  → Update repos/<repo-name>/tickets.md
  → Update repos/<repo-name>/findings.md with issue number

── STEP 10: UPDATE PLAN ──────────────────────────────────────────────
  repos/<repo-name>/plan.md
    → update fix priorities based on new and recurring findings
    → note cross-repo patterns if same issue exists in other repos

── STEP 11: WRITE DELTA ──────────────────────────────────────────────
  repos/<repo-name>/delta.md
    → new findings this run
    → recurring findings (previously seen, not yet fixed)
    → resolved since last run (fingerprint no longer detected)
    → risk score change vs previous run

── STEP 12: UPDATE INDEXES ───────────────────────────────────────────
  repos/_index.md
    → update this repo's row: risk score, finding count, ticket count, last run
  2-config/finding-id-registry.md
    → update counters for this repo to next available number per severity
    → add all new finding IDs to the "All Finding IDs Ever Assigned" table

── STEP 13: PRINT SUMMARY ────────────────────────────────────────────
  Print final run summary:
  - Repository reviewed
  - Files reviewed
  - Risk score (and delta vs last run)
  - New findings: X Critical, X High, X Medium, X Low
  - Recurring findings: X
  - Resolved since last run: X
  - GitHub Issues created: X (list each with ID + issue number)
  - GitHub Issues updated (merge/comment): X
  - GitHub Issues skipped (duplicates): X
```

---

## Commands

### "do review" or "review"
Review the first enabled repository in `deepscan.config.json`.
Follow the full execution order above.

### "review `<repo-name>`"
Review the named repository instead of the first one.
Same execution order.

### "review all"
Review all repositories in `deepscan.config.json` one by one.
Complete all 13 steps for each repo before starting the next.

### "do verification"
For each repo, open `repos/<repo-name>/verification.md`.
Apply `2-config/verification-rules.md` to each pending finding.
For confirmed findings, run the full duplicate check then create tickets.
For refuted findings, move to false-positives in findings.md.

### "re-review `<repo-name>`"
Re-run a full review on a repository that has already been reviewed.
Use this when the review plan has been updated or you want a fresh analysis.

Differences from a normal review:
- Read every file in the repository — same full two-phase approach
- Compare new findings against existing findings.md
- For findings that match existing ones: update Last Seen date only
- For findings that are new: assign new IDs and create tickets
- For findings from previous run not detected this run: mark as candidates for resolved
- When checking for duplicate tickets: match on file + issue type (not dimension)
  because the updated review plan may have recategorized some findings
- Update analysis.md, findings.md, delta.md with full new results

### "show status"
Read `repos/_index.md` and all `repos/<repo-name>/analysis.md` files.
Print: last run date, risk score per repo, active findings, open tickets.

### "show findings `<repo-name>`"
Read `repos/<repo-name>/findings.md`.
Print active findings table.

### "show tickets `<repo-name>`"
Read `repos/<repo-name>/tickets.md`.
Print open issues table.

### "show delta `<repo-name>`"
Read `repos/<repo-name>/delta.md`.
Print what changed in the latest run.

---

## Finding ID Format

`<ABBR>-<SEVERITY><n>`

| Part | Rule |
|---|---|
| ABBR | Split repo name on `-` and `_`, first letter of each word, uppercase |
| SEVERITY | C = Critical, H = High, M = Medium, L = Low |
| n | Sequential per repo per severity, zero-padded: 01, 02, 03... |

Always read current counters from `2-config/finding-id-registry.md`.
Never reuse a retired ID. Never assign a new ID to a recurring finding.

---

## Fingerprint Format

Every finding has a fingerprint used for deduplication across runs:

`<repo-abbreviation>:<file-path>:<dimension>:<issue-type>`

Examples:
- `BEB:src/auth/login.js:security:sql-injection`
- `US:controllers/user.py:code-quality:missing-error-handling`

Same fingerprint in a new run = recurring finding, not a new one.

---

## GitHub Issues — Label Rules

Every GitHub Issue must have exactly 3 labels applied:

| Label Type | Options | Rule |
|---|---|---|
| System | `deepscan` | Always applied |
| Priority | `P0-Critical` / `P1-High` / `P2-Medium` / `P3-Low` | Match severity |
| Category | `security` / `bug` / `performance` / `architecture` | Match dimension |

Category mapping:
- Security dimension → `security`
- Code Quality dimension → `bug`
- Performance dimension → `performance`
- Architecture dimension → `architecture`

Create labels on the repo before filing the first issue if they don't exist.
Label colors and full spec are in `2-config/ticket-rules.md`.

---

## Duplicate Prevention Summary

Never create a GitHub Issue without completing all 3 duplicate checks.
The full logic for each scenario (merge, update, reopen, adopt, skip) is in
`2-config/ticket-rules.md`. Always read that file before any ticket action.

The 3 checks in order:
1. Local `findings.md` — does this finding ID have an issue number already?
2. Local `tickets.md` — is this finding in any table (open, closed, skipped)?
3. GitHub MCP search — does an issue with `[<FINDING-ID>]` exist on the repo?

Only if all 3 return nothing → create a new issue.

---

## GitHub MCP Usage

- **Reading repos:** use GitHub MCP to read files, tree, and structure
- **Searching issues:** use GitHub MCP issue search before creating any ticket
- **Creating issues:** create on the individual repo (`github_url` in config)
- **Updating issues:** add comments only — never edit original issue body
- **Adding to board:** use `project_id` from `github_project` in config
- **Column:** use `default_column` from `github_project` in config

---

## Adding a New Repository

1. Add to `deepscan.config.json` under `repositories`:
   ```json
   {
     "name": "repo-name",
     "github_url": "https://github.com/your-org/repo-name",
     "stack": ["Language", "Framework", "DB"]
   }
   ```
2. Create `repos/<repo-name>/` with 6 files:
   `analysis.md`, `findings.md`, `verification.md`, `tickets.md`, `plan.md`, `delta.md`
3. Add abbreviation row to `2-config/finding-id-registry.md`
4. Run: `review <repo-name>`

---

## Reusing This Project as a Template

To start a new DeepScan project for different repositories:
1. Copy these 5 items into a new folder:
   - `CLAUDE.md`
   - `deepscan.config.json`
   - `0-meta/`
   - `1-review-plan/`
   - `2-config/`
2. Update `deepscan.config.json` with new repos and GitHub Project details
3. The `repos/` folder starts empty — do not copy it
4. Run: `do review`