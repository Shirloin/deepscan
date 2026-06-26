# Excluded Paths

Files and paths excluded from analysis in all repositories.
These supplement the `global_excluded_paths` in `deepscan.config.json`.

## Global Exclusions

```
node_modules/
dist/
build/
*.min.js
*.generated.*
__pycache__/
.env*
vendor/
migrations/
*.lock
*.snap
coverage/
.nyc_output/
```

## How Exclusions Work

1. Global exclusions here apply to every repository
2. Per-repository exclusions can be added in `deepscan.config.json` under each repo entry
3. Exclusions are path prefixes or glob patterns matched from the repo root
