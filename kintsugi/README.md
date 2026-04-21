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
3. **Show the seam** — leave the repair visible in code, commit,
   comment.

## Invariants

- Reproduction before fix.
- Failing test before fix code (not same commit, or at minimum written first).
- Visible seam — the fix is *labelled*, not hidden.
- Scope is the bug. Refactors are separate.

## Output artefacts

- A failing-test commit (or in-commit failing test).
- A fix commit using `templates/commit.md`.
- A comment / named constant / log pointing to the crack.
- A wrap-up bug report using `templates/bug-report.md` — goes in the
  issue, the PR description, or an incident log.

## Pairs with

- **Before kintsugi**: ordinary debugging. Kintsugi assumes you've
  localised the fault to ~1 module.
- **After kintsugi**: shokunin on the fix, to confirm the seam is
  kind and the gold is precise.
