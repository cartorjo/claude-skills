# Category 3 — Papers: Configuration & schemas

Kondo's papers category is the one people most under-triage because the
cost of mistakenly releasing a bill is high. Config is the same: a stray
`.env.example` or an unused CI job feels safer to leave than to remove.
That's exactly why this is category 3 — you need the earlier wins to
trust the trichotomy by the time you get here.

## Scope

- Top-level: `.env*`, `.editorconfig`, `.gitattributes`, `.gitignore`,
  lint configs, format configs, CI/CD workflows
- `config/`, `.github/`, `.gitlab/`, `.circleci/`, `.buildkite/`
- Schema files: JSON schemas, GraphQL schemas, migration files,
  `settings_schema.json` (Shopify)
- Locale / translation files (empty keys, orphaned namespaces)
- Feature-flag definitions with fully-on or fully-off values across all
  environments

## Detectors to run

- Config file staleness: last-touched date + whether any referenced keys
  appear in source.
- Unused environment variables: declared in `.env.example` but not read
  anywhere in source.
- Duplicate / overlapping configs: e.g. both `.prettierrc` and
  `prettier.config.js`; both `.eslintrc.json` and `.eslintrc.yml`.
- Dead CI jobs: jobs in workflow files that never trigger (conditions
  that always evaluate false, branch filters for deleted branches).
- Stale locale keys: keys present in locale files but never referenced
  by a translation call in source.
- Feature flags with the same value across all environments.

## Bucket rules

**Keep**
- Config with at least one active consumer in source (grep for the key /
  env var name returns non-zero).
- CI jobs that have run successfully in the last N days (default 90).
- Locale keys referenced by a `t()` / `| t` / `i18n.` call.

**Simplify**
- Duplicate config files (same tool, two formats). Action: pick the
  canonical one, delete the other. Evidence: both file paths.
- Feature flags with uniform value. Action: inline the branch matching
  the value; delete the flag definition. Evidence: flag name + its value
  across envs.
- CI job with conditions that always evaluate the same way. Action:
  remove condition or delete job. Evidence: the condition + actual
  trigger history.

**Release**
- Environment variable declared and never read. Evidence: grep for the
  var name across source + test + CI returns empty.
- Locale key never referenced. Evidence: grep for the key across source
  returns empty.
- CI job for a workflow / branch that no longer exists. Evidence: the
  workflow file path + the git ref that doesn't resolve.
- Schema migration files older than the data-retention horizon. Evidence:
  migration date + the retention policy.

**Kept on purpose**
- `.gitignore`, `LICENSE`, `.editorconfig`, `.gitattributes`, `CODEOWNERS`
  — release only with explicit user confirmation.
- Env vars read only at container-boot by infrastructure (not in source).
- Feature flags held deliberately at a uniform value during a migration.

## Evidence format

```
- locales/en.default.json key "old_feature.title" — released
  grep -r --include='*.liquid' --include='*.js' "'old_feature.title'\|\"old_feature.title\"\|t: old_feature.title" → 0 results
- .prettierrc.json + prettier.config.js — simplified (consolidate)
  keep: prettier.config.js (already loaded by editor config)
  drop: .prettierrc.json (superseded; last modified 2024-01-03)
```

## Commit subject

```
konmari(config): thank & release <N> — <short summary>
```

## This repo's pre-existing signal

Locale files under `locales/` (51 files, two schema-pair set: `de.json`
+ `de.schema.json`, etc.) — watch for `.schema.json` duplicates of keys
that moved to `.default.json`, and for orphaned keys in less-used
locales (bg, cs, el, hr, id, etc.). The `.github/workflows/theme-check.yml`
is actively run, keep. `.theme-check.yml` is tightly coupled to the
codebase and is a keep.
