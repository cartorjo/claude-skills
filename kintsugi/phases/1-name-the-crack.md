# Phase 1 — Name the crack

Before touching a line of code: reproduce, characterise, scope. No
fix yet. This phase's output is a paragraph and a reproduction, not a
patch.

## Steps

### 1.1 Reproduce

Get from "the user says it's broken" to "I can make it fail on demand".

- If a stack trace is given, identify the failing line and work
  backwards.
- If repro steps are given, follow them verbatim. Don't shortcut.
- If only a description is given, construct the minimum repro and
  confirm it with the user.
- If the bug is intermittent, characterise the rate (1 in N calls?
  only under load? only at specific times?).

No repro = no fix. If you can't reproduce after reasonable effort,
stop. Record what you tried and hand back.

### 1.2 Characterise

Write one paragraph answering:

- **Symptom**: what the user sees that they shouldn't.
- **Trigger**: the specific input / state / sequence that causes it.
- **Locus**: the module / function / line the defect lives in
  (probably — be honest about confidence).
- **Class of bug**: off-by-one? race? null deref? silent data
  corruption? performance cliff?

### 1.3 Scope

Explicit in-scope / out-of-scope list:

- **In scope** — the specific symptom + trigger combo. The narrowest
  fix that addresses it.
- **Out of scope** — every adjacent problem you noticed but are not
  fixing here. These go on a separate list. They may become separate
  kintsugi passes. They may be konmari-category-5 items. They don't
  sneak into this fix.

### 1.4 Decide

Three possible outcomes for phase 1:

- **Proceed** — repro is reliable, scope is clear, class of bug is
  known. Move to phase 2.
- **Pause** — repro is reliable but the fix needs a design decision
  (e.g., change a public API, touch a migration). Surface to user
  before phase 2.
- **Stop** — no repro, or the investigation revealed a wider issue
  than was reported. Hand back with the characterisation; do not
  guess-fix.

## Output

```
## Crack

**Symptom:** {what the user sees}
**Trigger:** {the specific input / sequence / state}
**Locus:** {file:line or module, with confidence level}
**Class:** {off-by-one / race / null / silent-corruption / perf / ...}

**Repro:**
  1. ...
  2. ...
  3. observe {the symptom}

**In scope:** {narrow description of the fix}
**Out of scope:** {adjacent issues observed but deferred}

**Decision:** {proceed / pause / stop} — {one-line reason}
```

## Anti-patterns

- Starting to write the fix "while I'm here". No. The phase's point is
  to resist that instinct.
- Fuzzy repro ("sometimes it fails"). Either characterise the rate or
  mark the bug intermittent; don't pretend you have repro when you
  don't.
- Scope creep disguised as "we should really also...". Write it down;
  keep this fix narrow.
