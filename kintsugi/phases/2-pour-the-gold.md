# Phase 2 — Pour the gold

The fix, preceded by the test that proves the fix works. In that
order.

## Steps

### 2.1 Failing test

Write a test that fails because of the bug, and *only* because of the
bug. Success criteria:

- Runs fast (seconds, not minutes).
- Targets the smallest unit that exhibits the bug (unit > integration
  > e2e, for this purpose).
- Named after what it asserts, not the defect. `it('handles empty
  tags array')` not `it('bug #4217')`.
- Fails *cleanly* — the failure message identifies what's wrong, not
  just that something is wrong.

Run it. Confirm it fails. Read the failure message — does it match
the crack's class from phase 1? If not, the test is catching something
else; rewrite.

Commit the failing test, if possible. If the repo's convention doesn't
allow red-CI commits on main branches, keep the failing test in the
working tree and note the intent — it still lands in the same PR as
the fix, just squashed.

### 2.2 Fix

Smallest fix that turns red to green. Guidelines:

- Change the fewest lines that address the crack. Not "while I'm
  here" — that violates scope from phase 1.
- Prefer the fix closest to the defect's origin. Defending downstream
  against bad upstream data is a valid pattern, but if the bug's
  origin is fixable, fix it at origin.
- If the fix touches > 1 module, pause and reconsider scope. Multi-
  module fixes often indicate a design problem disguised as a bug.
- Don't add defensive code in unrelated files "in case this happens
  again somewhere else". That's phase 3's seam, done wrong.

### 2.3 Verify

- Run the failing test: it passes.
- Run the full test suite for the affected module: no regressions.
- If the bug involved data (corruption, encoding, dates), think
  through what happens to records created during the buggy window.
  Data fixes may need a separate migration — note it in the out-of-
  scope list from phase 1.

## Output

- One (or more) commits introducing the failing test.
- One commit with the fix. Uses the commit template from
  `templates/commit.md`.

(Adjacent-risk sweep — "same defect shape elsewhere?" — happens in
phase 3, not here. Phase 2's scope is *this* bug.)

## Anti-patterns

- Fix first, test after. The test becomes a guard for the
  implementation you happened to write, not the behaviour you needed.
- "Passes now, works fine, moving on" without running adjacent tests.
- Catching the exception in a try/catch to make the crash go away.
  That's plastering, not gold.
