---
name: kintsugi
description: Bug-hygiene ritual. Three phases — name the crack, pour the gold, show the seam. Turns a bug fix into a durable artefact: reproduction, failing test that becomes the passing test, and a visible seam in code + commit + comment so future readers see where the repair is. Use when the user reports a bug, says "fix this bug", "/kintsugi", "investigate this issue", or posts a stack trace. Also suitable for post-incident write-ups.
---

# kintsugi — bug hygiene

The Japanese art of repairing broken pottery with gold lacquer, making
the repair more valuable than the original. Code bugs, done well,
produce the same result: the fix is more informative than the original
correct code would have been.

Three phases. In order. Skip none.

1. **Name the crack** — reproduce, characterise, scope. No fix yet.
2. **Pour the gold** — failing test first; fix second. In that order.
3. **Show the seam** — leave the repair visible in code, commit, and
   comment.

## Invariants

1. **Reproduction first.** No fix lands without a reliable repro.
   "Can't reproduce" is a valid phase-1 outcome; it means you stop
   there, not "you guess".
2. **Failing test before fix.** The test goes in a commit *before* the
   fix, or at worst in the same commit but written first. If the test
   passes before the fix, it isn't testing the bug.
3. **Visible seam.** Don't hide the repair. A comment, a named
   constant, a log, or a docstring points to the bug ticket / commit
   that motivated the fix. The seam is the artefact.
4. **Scope is the bug.** Kintsugi is not a refactor. If you see other
   problems, note them separately — don't fold them into this fix.

## Phases

Run in order. Each phase produces a concrete output.

1. `phases/1-name-the-crack.md` — reproduce, characterise, scope.
2. `phases/2-pour-the-gold.md` — failing test, then fix.
3. `phases/3-show-the-seam.md` — leave visible repair evidence.

## Workflow

### 0. Intake

From the user's report, extract:

- Observed behaviour (what happened)
- Expected behaviour (what should happen)
- Repro steps if known
- Environment / version if given
- Any logs, stack traces, screenshots

If any of these are missing, ask — don't guess. Ambiguous intake
produces wrong fixes.

### 1. Name the crack

Read `phases/1-name-the-crack.md`. Produce the phase-1 output: a
one-paragraph characterisation of the bug + a reliable repro +
explicit scope (what's in / out of this fix).

Stop. Confirm with user before moving to phase 2. If the crack turns
out to be bigger than thought, the scope statement is the artefact
that lets you negotiate.

### 2. Pour the gold

Read `phases/2-pour-the-gold.md`. Write the failing test first (commit
it, or at least save it). Run it — confirm it fails for the right
reason. Then write the fix. Run the test — confirm it passes.
Run adjacent tests — confirm no regression.

### 3. Show the seam

Read `phases/3-show-the-seam.md`. Leave the visible repair: a named
comment, a named constant, a log, or a CHANGELOG entry. Use the commit
template (`templates/commit.md`) with the crack / gold / seam shape.

### 4. Wrap

Produce the final artefact using `templates/bug-report.md` — useful
for sticking in an issue, the PR description, or an internal incident
log. It's also the durable version-two-you-reading-this-later document.

## When not to use

- Emergency fixes where the priority is restore-service, not artefact.
  Apply kintsugi *after* the hotfix, in a follow-up.
- Typo fixes / obvious one-character bugs. The ritual overhead exceeds
  the value.
- Bugs with no reproduction and no diagnostic lead. Close as
  "can't-reproduce", flag for monitoring, don't speculate.
