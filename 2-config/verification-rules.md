# Verification Rules

Claude reads this before deciding whether a finding is confirmed, refuted, or duplicate.

## Auto-Confirmed (no manual review needed)

These are confirmed immediately without going into a queue:
- Hardcoded credentials, API keys, or secrets in source code
- SQL/NoSQL injection via direct string concatenation with user input
- Packages with known CVEs (CVSS ≥ 4.0)
- Debug mode or verbose error output in production configuration files

## Confirmation Criteria

A finding is confirmed when ALL of these are true:
- The identified file and line actually exist in the repository
- The described issue pattern is genuinely present in the code
- The code is in production/runtime files (not test-only, not commented out)
- The issue is not already mitigated at another layer (WAF, API gateway, etc.)
- The issue is not intentionally documented as a known accepted trade-off

## Refutation Criteria

A finding is refuted when ANY of these are true:
- The file or function no longer exists in the current codebase
- The issue exists only in test or mock files
- The issue is fully mitigated at the infrastructure layer
- The AI misread the code context on closer inspection
- It is an exact duplicate of an already-confirmed finding (same file, same issue type)

## Severity Override Rules

| Condition | Action |
|---|---|
| Finding affects user authentication or session management | Upgrade to Critical if not already |
| Finding affects payment or financial data | Upgrade to Critical if not already |
| Finding exists only in test code | Downgrade to Low or refute |
| Finding is theoretical with no realistic attack vector | Downgrade one level |
| Finding has a public CVE assigned | Match or upgrade to CVE severity |
