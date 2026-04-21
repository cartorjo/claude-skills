# Category 2 — Books: Documentation & prose

Kondo's books category is about your relationship with information you
"meant to get to". Docs are the same: the README that hasn't been
touched since rewrite 2, the worklog from the migration that shipped, the
TODO note someone left three years ago. Releasing these isn't about
being dismissive of the work — it's about not lying to the next reader.

## Scope

- Top-level: `README.md`, `CHANGELOG.md`, `CONTRIBUTING.md`,
  `release-notes.md`
- `docs/` and subdirs
- `.claude/WORKLOG.md`, `.claude/HARMONIZATION-REPORT.md`, and any other
  per-session reports under `.claude/`
- Inline prose comments marked `TODO`, `FIXME`, `XXX`, `HACK` in source
  files

## Detectors to run

- Modification time + git last-touched for each doc file; flag docs that
  haven't moved in N days (default 180) as staleness candidates.
- Links from `README.md` → resolve relative paths; broken internal links
  are staleness evidence.
- `TODO/FIXME/XXX/HACK` grep across source, with git blame date;
  anything older than N days is an aging annotation.
- Duplicate prose: two docs describing the same subsystem with
  contradictions (e.g. README says "uses X", docs/architecture says
  "uses Y").

## Bucket rules

**Keep**
- Doc referenced by onboarding, by CI, or by external links (check `gh`
  issues / PRs for mentions if available).
- Unique and current: no other doc covers the same subject.

**Simplify**
- Doc currently accurate but structurally redundant with another. Action:
  merge, or pick one canonical source and make the other a one-line
  pointer.
- Session / worklog reports that are historically useful but no longer
  active. Action: move under `.claude/history/<date>/` (or equivalent
  archive path). Keeps the artefact, clears the active surface.
- Aging `TODO/FIXME` annotations. Action: resolve, convert to a tracked
  issue, or delete the comment. Don't leave a 2-year-old TODO as
  decoration.

**Release**
- Doc describing a feature that was removed. Evidence: the commit that
  removed the feature + the fact the doc still references it.
- Changelog entries older than the current major version's "support
  horizon". Evidence: semver policy + entry date.
- Broken-internal-link docs with no salvageable content. Evidence: the
  broken link targets.

**Kept on purpose**
- `LICENSE.md` — never touch.
- Docs with external inbound traffic (check analytics / search-console
  if available, or ask the user).
- Historical reports someone asked to preserve verbatim.

## Evidence format

```
- docs/old-architecture.md — released
  describes "X subsystem" removed in commit abc1234
  last modified: 2023-05-12 (> 180 days)
- .claude/WORKLOG.md — simplified (archived)
  move to .claude/history/2026-04-16/WORKLOG.md
  reason: phase-complete artefact, not active work log
```

## Commit subject

```
konmari(docs): thank & release <N> — <short summary>
```

## This repo's pre-existing signal

`.claude/WORKLOG.md` and `.claude/HARMONIZATION-REPORT.md` are Category 2
Simplify candidates — phase-complete reports, strong candidates for
archival under `.claude/history/2026-04-16/`. The top-level
`release-notes.md` should be checked for entries superseded by shipped
features.
