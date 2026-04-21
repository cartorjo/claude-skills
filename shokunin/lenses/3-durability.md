# Lens 3 — Durability

Will this still be right in six months? In a year? With a new consumer
the author didn't know about?

Durability isn't about predicting the future. It's about not making
promises the code can't keep.

## Checks

- **Implicit contracts**: what assumptions does this code make that
  aren't enforced? (Order-of-calls, key presence, caller trust,
  singleton-ness.)
- **Rotting inputs**: data formats, API schemas, date formats,
  character encodings. Is there a version pin or an assertion that
  catches drift?
- **Tests that protect behaviour, not implementation**: does a
  refactor break the tests, or only a regression? If refactor breaks
  them, they're brittle.
- **Hidden coupling**: does this change depend on something that isn't
  imported? (Global state, env vars, side-effecting module loads.)
- **Flakiness surface**: time, random, filesystem, network, ordering.
  Each is a future "intermittent failure" bug unless explicitly
  handled.
- **Documentation half-life**: comments or docs that describe a
  current state and will be wrong the moment the code changes
  ("returns null in case X" — said without also asserting it).

## Severity guide

**must-change**
- Relies on an implicit contract the code doesn't assert (will silently
  corrupt when the contract is violated).
- Test depends on ordering / timing / random seed without stabilising.
- Introduces a new source of non-determinism without isolation.

**consider**
- Comment describes current behaviour without a test guarding it.
- New dependency on an unpinned external service / format.
- Behaviour that's right today, subtly wrong after the next adjacent
  change.

**observed**
- Code that's correct today under current usage but thin on protection
  for a different usage.
- Test coverage correct but narrow — happy path only.

## Evidence

Cite the assumption and the failure mode it creates. "If X changes,
this silently returns stale data" is actionable. "This could break"
isn't.

## What good looks like

- The code fails loudly at the earliest point it can detect the
  problem.
- Tests exercise the *contract*, not the *implementation*.
- Dependencies are named and versioned; coupling is visible, not
  ambient.
