---
name: konmari
description: Marie Kondo-inspired codebase declutter. Scans category by category (assets, docs, config, code, legacy) and recommends Keep / Simplify / Release for each finding, with evidence. Dry-run by default; one category, one commit, one approval. Use when the user invokes `/konmari`, asks to "declutter", "tidy up", "clean up the codebase", or do a "code spring clean" — and for targeted single-category passes like "konmari the dependencies".
---

# Konmari — codebase declutter

Kondo's method, applied to code. Five categories in fixed order, easiest first.
Each category produces a dry-run report with three buckets (Keep / Simplify /
Release), each finding cites evidence, and nothing is deleted until the user
explicitly approves that category.

## Invariants

These are non-negotiable. Violating any of them makes the skill worse than
doing nothing.

1. **One category at a time.** Finish the current category fully before
   starting the next. Kondo's rule; translates directly into reviewable,
   atomic commits.
2. **Dry-run is the default.** Producing a report is not applying it.
   Applying requires a separate, explicit approval from the user.
3. **Evidence or it doesn't move.** Every "Release" cites the grep command
   that returned zero, or the file/commit that supersedes it. Every
   "Simplify" cites the file:line pairs that form the duplicate cluster. "Looks
   unused" is not evidence and does not go on the list.
4. **Explicit keeps.** Each report's "Kept on purpose" section names things
   you considered releasing but didn't. This is how the rationale survives
   into the next pass.
5. **One commit per category.** Format in `templates/commit.md`. Body lists
   each released item with served-for / retired-because.
6. **Non-destructive dry-run.** After a dry-run, `git status` is clean.

## Workflow

### 0. Initial handshake

On invocation (whether via `/konmari`, auto-trigger, or an explicit request
like "declutter the codebase"):

- Check for existing state at `.claude/konmari/state.json`. If present, offer
  to resume at `current_category` instead of restarting. If the user asks for
  a specific category (e.g. "konmari the dependencies"), skip to that one and
  skip the resume prompt.
- Detect the ecosystem. Probe for these markers at repo root:
  - `*.liquid`, `.theme-check.yml`, `sections/`, `snippets/` → load `detectors/liquid.md`
  - `package.json` → load `detectors/node.md`
  - `pyproject.toml` or `requirements.txt` → load `detectors/python.md`
  - `Cargo.toml` → load `detectors/rust.md`
  - `go.mod` → load `detectors/go.md`
- Load **only** the matching detector file(s). Do not load the others.
- Read `templates/report.md` and `templates/commit.md` so output shape is
  consistent before producing the first report.

### 1. Per-category loop

For each category 1 → 5 in order:

1. Read `categories/<N>-<name>.md`. It lists the detectors to run, the
   Keep / Simplify / Release rules for that category, and anything
   category-specific.
2. Run detectors. Collect findings.
3. Assign each finding to exactly one bucket:
   - **Keep** — actively used. One-line note: what it earns.
   - **Simplify** — used but bloated: duplicate cluster, one-line wrapper,
     dead branch, over-abstracted helper. Cite file:line pairs.
   - **Release** — zero inbound references, or fully superseded. Cite the
     grep scope + result, or the superseding file/commit.
4. Emit the report using `templates/report.md`. Include the "Kept on purpose"
   section even if it's short — that's the point.
5. **Stop.** Ask the user: apply, skip, or stop here. Do not proceed to the
   next category without an answer.
6. If the user approves **apply**:
   - Make the edits.
   - Single commit using `templates/commit.md` format.
   - Update `.claude/konmari/state.json`: add N to `completed_categories`,
     set `current_category` to N+1.
7. If the user approves **skip**: just advance `current_category`. No commit.
8. If the user says **stop**: write the state file and exit cleanly.

### 2. Wrap-up

After category 5 (or when the user stops):

- Produce a final summary using the same `templates/report.md` shape but
  aggregated: total Kept / Simplified / Released across all completed
  categories.
- Include a top-level "Kept on purpose" that repeats the key explicit-keeps
  from each category. This is the durable artefact — the equivalent of
  `HARMONIZATION-REPORT.md` in this repo's history.
- Leave state file alone so follow-up sessions can inspect what was done.

## Category map

| # | Kondo | Code analog | Playbook file |
|---|---|---|---|
| 1 | Clothes | Deps & static assets | `categories/1-assets.md` |
| 2 | Books | Docs & prose | `categories/2-docs.md` |
| 3 | Papers | Config & schemas | `categories/3-config.md` |
| 4 | Komono | Code & styles | `categories/4-code.md` |
| 5 | Sentimental | Legacy & compat | `categories/5-legacy.md` |

Order is Kondo's. Do not change it. Easy categories build momentum; the
sentimental category only works once the earlier ones have trained the eye.

## Trichotomy rules (cross-cutting)

Referenced by every category playbook; stated once here.

**Keep**
- Actively referenced (grep returns non-zero, import graph reaches it, route
  registers it, template renders it).
- Or irreplaceable and unique (one-of-a-kind, no near-duplicate).
- Required evidence: a one-line "what it earns" note.

**Simplify**
- Referenced, but one of:
  - Duplicate cluster: ≥ 2 near-identical blocks. Evidence: file:line pairs.
  - One-line wrapper around a stdlib / framework call. Evidence: the wrapper
    body.
  - Dead branch: reachable but never executed (flag permanently on/off,
    condition always true/false). Evidence: the condition and the flag value.
  - Over-abstracted: indirection without ≥ 2 concrete consumers. Evidence:
    the single consumer.
- Required evidence: one of the above, concrete.

**Release**
- Zero inbound references. Evidence: the exact grep command run and the empty
  result.
- Or superseded. Evidence: the replacement file + the commit sha where it
  took over.
- Required evidence: the grep or the supersession pointer. Both must be
  reproducible.

## State file

Path: `.claude/konmari/state.json`. Shape in `state-schema.json`. Git-ignored.
The skill writes it after each category's approval decision. If the skill
can't write to `.claude/` (read-only repo, permissions), fall back to
`$TMPDIR/konmari-<repo-basename>.json` and tell the user.

## Adapting to the current repo

The detector files know about ecosystems, not this specific repo. Always
check for repo-specific signals first and factor them into the report:

- A `WORKLOG.md` or harmonization/cleanup report at the repo root or under
  `.claude/`: read it. Items flagged as "deferred" or "known remaining" are
  strong Simplify / Release candidates — the previous pass already did the
  triage.
- A `.theme-check.yml`, `.eslintrc`, `ruff.toml`, etc: mine it for asset-size
  budgets, ignore paths, severity gates. Use those thresholds directly in
  Category 1 and 3 reports; don't invent new numbers.
- A `CHANGELOG.md` or `release-notes.md`: use "Removed" / "Deprecated"
  entries as Category 5 seeds.
