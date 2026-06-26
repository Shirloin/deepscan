# Ticket Rules

Claude reads this file before creating, updating, or skipping any GitHub Issue.
Never create a ticket without completing the duplicate check sequence first.

---

## Before Creating Any Ticket — Mandatory Duplicate Check

Run these 3 checks in order for EVERY finding before creating an issue.
Stop at the first match and follow the action for that check.

### Check 1 — Local findings.md

Open `repos/<repo-name>/findings.md` for this repository.
Look for the finding ID in the Active Findings table.

| Condition | Action |
|---|---|
| Finding ID exists AND has a GitHub Issue number | → Go to **Merge/Update Logic** |
| Finding ID exists AND GitHub Issue column is `—` | → Continue to Check 2 |
| Finding ID does not exist yet | → Continue to Check 2 |

---

### Check 2 — Local tickets.md

Open `repos/<repo-name>/tickets.md` for this repository.
Check all three tables: Open Issues, Closed Issues, Skipped.

| Condition | Action |
|---|---|
| Finding ID found in Open Issues table | → Go to **Merge/Update Logic** |
| Finding ID found in Closed Issues table | → Go to **Reopen Logic** |
| Finding ID found in Skipped table | → Go to **Skip Re-evaluation Logic** |
| Finding ID not found in any table | → Continue to Check 3 |

---

### Check 3 — GitHub Issues via MCP (source of truth)

Search the repository's GitHub Issues using the GitHub MCP server.

Search query: `[<FINDING-ID>]` in issue titles
Example: search for `[BEB-H01]` in `backend-easy-buy` issues.

Also search by finding title keywords as a secondary search in case the
issue was created manually without the finding ID in the title.

| Condition | Action |
|---|---|
| Open issue found with matching finding ID in title | → Go to **Merge/Update Logic** |
| Closed issue found with matching finding ID in title | → Go to **Reopen Logic** |
| Open issue found with same topic but no finding ID | → Go to **Adopt Logic** |
| No matching issue found anywhere | → **Create new issue** (see Creation Rules) |

---

## Merge / Update Logic

Use this when an open GitHub Issue already exists for this finding.

**When to merge vs update:**

| Situation | Action |
|---|---|
| Same finding, same file, same issue type — no new information | Skip. Add note to `tickets.md`: "Issue #N already open, no new information" |
| Same finding, same file — but new evidence or additional affected files found | Update the existing issue (see below) |
| Same finding type, different file — genuinely different location | Create a new issue with a new Finding ID |
| Two findings that describe the same root cause from different angles | Merge into one issue (see below) |

**How to update an existing issue:**

1. Read the existing issue body via GitHub MCP
2. Compare with current finding details
3. If new information exists (new affected file, new evidence, updated line number, broader scope):
   - Add a comment to the existing issue — do NOT edit the original body
   - Comment format:
     ```
     ## DeepScan Update — <YYYY-MM-DD>

     **Run:** #<n>
     **Update type:** Additional evidence / Broader scope / New affected file

     <Description of what is new or different from the original finding>

     **Additional affected files:** `<file>`, `<file>` (if applicable)
     **Updated line reference:** <line> (if changed)
     ```
4. Update `repos/<repo-name>/tickets.md` — add note in the finding row: "Updated <date>"
5. Update `repos/<repo-name>/findings.md` — update `Last Seen` date

**Do NOT:**
- Edit the original issue body
- Create a duplicate issue
- Close and reopen the issue just because it appeared again in a scan

---

## Reopen Logic

Use this when a GitHub Issue exists but is **closed**, and the finding
is detected again in the current run.

1. Search for the closed issue via GitHub MCP
2. Add a comment — do NOT reopen automatically:
   ```
   ## DeepScan Detected Again — <YYYY-MM-DD>

   **Run:** #<n>
   **Finding ID:** <FINDING-ID>

   This issue was detected again in the latest DeepScan run.
   It was previously closed on <closed-date>.

   Please verify whether the fix is complete or if this is a regression.
   Reopen this issue if the problem has returned.
   ```
3. Update `repos/<repo-name>/findings.md`:
   - Move finding from Resolved back to Active
   - Update `Last Seen` date
   - Add note: "Recurred after closure — see issue #N comment"
4. Update `repos/<repo-name>/tickets.md`:
   - Move from Closed Issues back to Open Issues
   - Add note: "Recurred <date>, comment added"

---

## Adopt Logic

Use this when a GitHub Issue exists on the repo that covers the same
problem but was created manually without a DeepScan finding ID.

1. Read the existing issue to confirm it covers the same problem
2. Add a comment to adopt it:
   ```
   ## DeepScan — Adopting Existing Issue — <YYYY-MM-DD>

   **Finding ID:** <FINDING-ID>
   **Severity:** <severity>
   **Dimension:** <Security / Code Quality / Architecture / Performance>

   DeepScan detected the same issue in run #<n>.
   This finding has been linked to this existing issue.

   No duplicate issue will be created.
   ```
3. Apply DeepScan labels to the existing issue: `deepscan`, priority label, category label
4. Add the issue to the GitHub Project board if not already there
5. Record in `repos/<repo-name>/tickets.md` with the existing issue number
6. Record in `repos/<repo-name>/findings.md` with the existing issue number

---

## Skip Re-evaluation Logic

Use this when a finding was previously skipped (in the Skipped table of tickets.md).

Read the skip reason:

| Previous Skip Reason | Action |
|---|---|
| `DUPLICATE` | Run Check 3 again — the original issue may now be closed |
| `FALSE-POSITIVE` | Do not create ticket. Finding should be in false-positives table |
| `OUT-OF-SCOPE` | Do not create ticket unless scope has changed in config |
| Any other reason | Evaluate against current ticket rules and create if now qualifies |

---

## Creation Rules (only reached after all 3 checks pass with no match)

ALL severity levels get a GitHub Issue immediately on detection.

| Severity | GitHub Issue | Priority Label |
|---|---|---|
| Critical | Yes — immediately | `P0-Critical` |
| High | Yes — immediately | `P1-High` |
| Medium | Yes — immediately | `P2-Medium` |
| Low | Yes — immediately | `P3-Low` |

---

## GitHub Issue Format

Every new issue MUST follow this format exactly.

**Title:** `[<FINDING-ID>] <Finding Title>`

**Labels — apply all three types:**

| Label Type | Options | Rule |
|---|---|---|
| System | `deepscan` | Always |
| Priority | `P0-Critical` / `P1-High` / `P2-Medium` / `P3-Low` | Match severity |
| Category | `security` / `bug` / `performance` / `architecture` | Match dimension |

Category mapping:
- Security dimension → `security`
- Code Quality dimension → `bug`
- Performance dimension → `performance`
- Architecture dimension → `architecture`

**Body:**

```
## Bug Description
<What the issue is and why it matters. Mention the related file(s) and
function/class name inline where relevant.
Example: "In `src/auth/login.js`, the `authenticateUser()` function
concatenates user input directly into a SQL query string.">

## Steps to Reproduce
1. <Step one — reference the specific file, endpoint, or function>
2. <Step two>
3. <Step three — what triggers the issue>
Note: Related files involved in this flow: `<file1>`, `<file2>` (if applicable)

## Expected Behavior
<What should happen instead — the correct or secure pattern.
Reference the file or function where the fix should be applied if helpful.>

## Actual Behavior
<What is currently happening. Include file path and line number.
Example: "In `src/auth/login.js` line 47, the query is built as:
`query = 'SELECT * FROM users WHERE email=' + userInput`
which allows an attacker to inject arbitrary SQL.">
```

---

## After Creating Each Issue

1. Note the issue number returned by GitHub MCP
2. Add issue to the GitHub Project board:
   - Use `project_id` from `deepscan.config.json`
   - Set column to `default_column` from config (default: "Backlog")
3. Create required labels on the repo if they don't exist yet:
   `deepscan`, `P0-Critical`, `P1-High`, `P2-Medium`, `P3-Low`,
   `security`, `bug`, `performance`, `architecture`
4. Update `repos/<repo-name>/tickets.md` — add to Open Issues table
5. Update `repos/<repo-name>/findings.md` — fill in GitHub Issue column

---

## Label Creation (if labels don't exist on the repo)

Create these labels before filing the first issue on any repository:

| Label | Color | Purpose |
|---|---|---|
| `deepscan` | `#0e8a16` (dark green) | Identifies DeepScan-created issues |
| `P0-Critical` | `#b60205` (dark red) | Critical severity |
| `P1-High` | `#e4430a` (orange-red) | High severity |
| `P2-Medium` | `#e4a820` (yellow) | Medium severity |
| `P3-Low` | `#1d76db` (blue) | Low severity |
| `security` | `#d93f0b` (red) | Security dimension |
| `bug` | `#fc2929` (bright red) | Code quality dimension |
| `performance` | `#5319e7` (purple) | Performance dimension |
| `architecture` | `#006b75` (teal) | Architecture dimension |