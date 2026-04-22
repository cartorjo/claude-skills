# Lens 5 — Silence

No ceremonial code. No over-comments. No dead branches. No noise.

Silence is the hardest lens because it's about what *isn't* there.
The craftsman's instinct: remove anything that doesn't earn its place.

## Checks

- **Comment that narrates the code**: `// increment i` above `i++`.
  Delete.
- **Docstring that says the same thing as the signature**: `/**
  Returns the user */ function getUser()`. Delete the docstring.
- **Defensive code for impossible cases**: null-checks on values the
  type system guarantees are non-null. Validation on internal callers
  that can't violate it.
- **Ceremonial wrappers**: `function addOne(x) { return x + 1; }` at
  the callsite of `x + 1`. Inline.
- **Dead branches**: `if (DEBUG)` where `DEBUG` is always false.
  `if (feature_on)` where the flag has been fully on for six months.
- **Commented-out code blocks**: the git history is the archive.
- **`// TODO` without a date or ticket**: either do it or track it,
  don't leave it as decoration.
- **Redundant type annotations**: where inference would give the same
  result and nothing is clarified.
- **Over-logging**: `log.debug('entering function')` on every
  function.
- **Test that asserts the thing the framework already asserts**
  (e.g., testing that a mock was called on a line where the mock
  would throw if not called).

## Severity guide

**must-change**
- Commented-out code.
- Dead branch for a permanently-set flag.
- Narration comment that duplicates the code.

**consider**
- Defensive code for cases the type system rules out.
- One-line wrapper with ≤ 1 consumer.
- Redundant type annotations (language-dependent — stylistic).

**observed**
- A docstring that mostly restates the signature but adds minor
  context.
- Slightly verbose test setup.

## Evidence

Paste the offending snippet. Show the shorter version as the fix.

## What good looks like

- Every comment explains a *why* that wouldn't be obvious from
  reading the code.
- Every line pays rent. Delete any that don't.

## A warning

Silence overreaches easily. Novices strip useful comments along with
ceremonial ones. The test: does the comment / annotation / check
protect against a real mistake someone could make? If yes, keep. If
it's just prose paraphrasing the code, delete.
