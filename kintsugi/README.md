# kintsugi

Bug-hygiene ritual. Three phases that turn a fix into a durable
artefact.

## Install

```bash
cp -r kintsugi ~/.claude/skills/
```

## Invoke

- `/kintsugi` with a bug report, stack trace, or reproduction.
- "fix this bug" / "investigate this issue" / "I think there's a bug
  in X" — skill auto-triggers.

## The three phases

1. **Name the crack** — reproduce, characterise, scope. No fix yet.
2. **Pour the gold** — failing test first; fix second. In that order.
3. **Show the seam** — anchor the repair in test name, commit body,
   changelog; sweep for adjacent risks.

## Invariants

- Reproduction before fix.
- Failing test before fix code (not same commit, or at minimum written first).
- Seam lives where it won't rot — test name, commit body, changelog.
  Code-level seams (named constant / helper / comment) only when the
  fix introduces a value or choice whose rationale the code alone
  can't carry.
- Scope is the bug. Refactors are separate. Defensive assertions across
  "similar" code are scope creep, not gold.

## Output artefacts

- A failing-test commit (or in-commit failing test) with a
  behaviour-describing test name — this is the primary seam.
- A fix commit using `templates/commit.md` (crack / gold / seam body).
- A changelog / doc entry **if** the fix changed a user-observable
  contract.
- A named constant, helper, or narrow comment **only** when earned by
  the fix — not as default decoration.
- A wrap-up bug report using `templates/bug-report.md` — goes in the
  issue, the PR description, or an incident log.

## Pairs with

- **Before kintsugi**: ordinary debugging. Kintsugi assumes you've
  localised the fault to ~1 module.
- **After kintsugi**: shokunin on the fix, to confirm the seam is
  kind and the gold is precise.
