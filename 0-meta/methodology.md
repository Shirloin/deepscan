# Methodology

## What We Review

Each repository is analyzed across four dimensions:

| Dimension | What We Look For |
|---|---|
| **Code Quality** | Dead code, duplication, naming conventions, complexity, readability, test coverage gaps |
| **Security** | OWASP Top 10, injection risks, authentication/authorization flaws, secrets in code, dependency vulnerabilities |
| **Architecture** | Coupling, separation of concerns, API contract consistency, dependency direction, scalability risks |
| **Performance** | N+1 queries, missing indexes, unbounded loops, blocking calls, memory leaks, caching gaps |

## How Analysis Is Done

- Tool: Claude Code in headless mode via GitHub MCP Server
- Model: claude-sonnet-4-6
- Trigger: Manual — run DeepScan on demand
- Scope: All repositories listed in [scope.md](scope.md)
- Output: One analysis `.md` per repository per run, stored in `1-analysis/repos/<repo>/runs/`

## How Findings Are Created

1. Raw analysis output is parsed for distinct issues
2. Each issue is fingerprinted by: repo + file path + issue category + issue type
3. Fingerprint is checked against [finding-id-registry.md](finding-id-registry.md)
   - If new → assign new Finding ID, create finding file
   - If existing → update "last seen" date only
4. Delta report is generated comparing this run to last run

## When Tickets Are Created

See [7-automation/ticket-rules.md](../7-automation/ticket-rules.md) for full rules.

Summary:
- Critical/High → ticket created immediately after verification
- Medium → ticket created if finding appears 2+ consecutive weeks
- Low → logged in DeepScan only, no ticket unless escalated

## Run Schedule

| Day | Action |
|---|---|
| Monday 00:00 | Automated scan runs |
| Monday morning | Delta report reviewed |
| Monday | New findings enter verification queue |
| Wednesday | Verified findings get GitHub Issues created |
| Friday | Trends and fix rate updated |
