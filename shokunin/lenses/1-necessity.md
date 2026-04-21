# Lens 1 — Necessity

Does this code need to exist?

The easiest code to defend is the code that isn't there. Look for the
stuff that could be absent and nothing would change.

## Checks

- **Existing-feature check**: does the repo already do this, elsewhere?
  Grep for adjacent verbs (e.g., a new `formatPrice()` when `money()`
  exists).
- **Framework-native check**: is the change re-implementing something
  the framework / stdlib already provides? (e.g., a hand-rolled debounce
  when `lodash.debounce` is already in deps.)
- **Dead paths**: does the change add a branch that can't be reached, or
  an export with no consumer?
- **Speculative generality**: does it abstract for consumers that
  don't exist yet? ("We might want to swap this out one day.")
- **Flag / config surface**: does it add a knob no one has asked for?

## Severity guide

**must-change**
- Adds a duplicate of an existing function / utility with identical
  semantics.
- Adds a public API surface with no internal consumer.
- Adds a feature flag with no rollout plan.

**consider**
- Mild duplication with adjacent code that *could* be unified.
- One-of-a-kind abstraction without a second consumer yet.
- An option parameter that no caller sets non-default.

**observed**
- A helper that's lightly speculative but harmless.
- Naming overlaps with existing code but semantics differ — worth
  noting for the next reader.

## Evidence

Cite the existing thing: `file:line` of the prior implementation, or
the docs reference for the framework-native equivalent.

## What good looks like

- Every new entity (function, class, file, export) has a named concrete
  consumer in the same change or a same-sprint follow-up.
- Delta to the public API surface is zero unless justified.
- The diff shrinks one place for every place it grows (subtractive
  changes are a craftsman signal).
