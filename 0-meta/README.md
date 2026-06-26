# DeepScan

DeepScan is a weekly automated code quality and security review system.

It scans multiple GitHub repositories, identifies findings across quality, security, architecture, and performance dimensions, verifies them, and automatically creates GitHub Issues on the relevant repository with the correct priority.

## How It Works

1. **Analysis** — Every week, each repository in scope is scanned using AI-assisted review via Claude Code + GitHub MCP Server
2. **Findings** — Each issue discovered is assigned a stable Finding ID and logged
3. **Verification** — New findings are verified before a ticket is created (confirmed, refuted, or pending)
4. **Tickets** — Confirmed findings become GitHub Issues on the repository and are added to the GitHub Project backlog
5. **Trends** — Each week's results are compared to the previous week to detect regressions and track fix rates

## Navigation

| Section | Purpose |
|---|---|
| [0-meta](../0-meta/) | System documentation, scope, methodology |
| [1-analysis](../1-analysis/) | Raw weekly analysis per repository |
| [2-findings](../2-findings/) | All findings with stable IDs, status, and history |
| [3-verification](../3-verification/) | Verification queue, results, and rules |
| [4-tickets](../4-tickets/) | GitHub Issue tracking and ticket log |
| [5-plan](../5-plan/) | Fix priorities, work packages, blockers |
| [6-trends](../6-trends/) | Weekly scores, regressions, fix rate |
| [7-automation](../7-automation/) | Config, rules, run log |

## Finding ID Format

`<REPO>-<SEVERITY><n>` — e.g., `US-C01`, `BEB-H03`, `FEB-M11`

Severity: C = Critical, H = High, M = Medium, L = Low

## GitHub Issue Priority Mapping

| Finding Severity | GitHub Issue Priority |
|---|---|
| C — Critical | P0-Critical |
| H — High | P1-High |
| M — Medium | P2-Medium |
| L — Low | P3-Low |
