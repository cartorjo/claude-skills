# Category 5 — Sentimental: Legacy & compatibility

Kondo leaves sentimental items for last because by then you've practised
the decision dozens of times and won't be ambushed by feeling. Legacy
code is the same: the polyfill for a browser no one supports, the
feature-flag fallback for the rollout that finished six months ago, the
compat shim for the old DB schema. You need the earlier four categories'
practice before you can see these clearly.

## Scope

- Polyfills / shims for browsers / runtimes no longer supported
- Feature-flag code paths that are fully on / fully off in every env
  (fallback-only branches, or never-reached variant branches)
- Compatibility layers for removed APIs / schemas / data formats
- Migration helpers from a migration that has fully completed
- "Just in case" defensive code without a concrete risk
- Deprecated export aliases kept "for backward compat" past the support
  horizon

## Detectors to run

- Cross-reference feature-flag definitions (from Category 3) with their
  consumer branches. Fully-on flag → delete the fallback branch.
  Fully-off flag → delete the variant branch.
- Polyfill libs in deps + comments like "for IE11", "for old Safari";
  check current browser-support policy.
- Comments containing "legacy", "deprecated", "TODO remove after",
  "kept for backward compat" — with git blame, compare against the
  referenced date / version.
- Aliases: `export { X as Y }` where `Y` is not imported anywhere.
- Migration helpers: scripts under `scripts/migrations/` or similar with
  a "ran on" date older than the current major version.

## Bucket rules

**Keep**
- Shim / polyfill matching a still-supported runtime target. Evidence:
  support policy + runtime version.
- Feature-flag branch where the flag actually varies across envs or users.
  Evidence: flag config showing non-uniform value.
- Migration helper still referenced by operational playbook.

**Simplify**
- Feature-flag branch whose flag is uniformly on. Action: inline the
  on-branch, delete the flag. Evidence: flag + env values.
- Deprecated alias with ≤ 1 external consumer. Action: update the
  consumer, delete the alias. Evidence: consumer location.
- Compat layer with a clearly documented sunset date that has passed.
  Evidence: the comment + the date.

**Release**
- Polyfill for a runtime outside the declared support policy. Evidence:
  support policy + polyfill target.
- Feature-flag branch for a flag fully off in every env. Evidence:
  flag + env values + the dead branch.
- Aliased export with zero consumers. Evidence: the import grep.
- Migration helper for a migration that's fully rolled out + no rollback
  path needed. Evidence: migration completion marker.

**Kept on purpose**
- Graceful-degradation code for documented edge cases (low-memory,
  no-JS, reduced-motion). These aren't legacy — they're ongoing
  requirements.
- Compat shims explicitly supported by a public contract (SDK
  backward-compat guarantee). Release only with a major-version bump.
- Code tagged `// intentional: see <ticket>` or equivalent.

## Evidence format

```
- src/compat/ie11-polyfill.ts — released
  Browserslist target excludes IE11 (package.json browserslist field, commit abc1234)
- src/flags/new_checkout.ts + fallback branch in src/checkout/index.ts:83-140 — simplified
  flag fully on in dev / staging / prod (config/flags.json, all-env = true)
  action: inline new-checkout path, delete fallback, delete flag definition
```

## Commit subject

```
konmari(legacy): thank & release <N> — <short summary>
```

## This repo's pre-existing signal

Likely thin — Horizon is modern, the repo is young, and the harmonize
pass already cleared obvious dead code. Areas worth probing:

- `HARMONIZATION-REPORT.md` "Known Remaining Issues" #3 (Typekit GDPR
  gating) is a decision to make, not a legacy item.
- `HARMONIZATION-REPORT.md` "What Was Skipped (Already Handled by
  Horizon)" lists items Horizon does differently — check if any old
  shims snuck back in.
- Any `{% comment %}` blocks in Liquid files longer than 3 lines are
  candidates.
