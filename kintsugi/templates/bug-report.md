# Kintsugi bug-report template

Use for PR descriptions, issue write-ups, or internal incident logs.
The same shape serves all three. Fill in after phase 3 completes.

---

## {Short title — what now works that didn't}

### Crack

**Symptom:** {what the user saw}
**Trigger:** {the specific input / state / sequence}
**Scope of impact:** {who / how many / since when, if known}
**Class:** {off-by-one / race / null / silent-corruption / perf / other}

### Repro

1. …
2. …
3. Observe {the symptom}

**Environment:** {version / OS / runtime / browser, if relevant}

### Gold

{1–3 sentences describing the fix. Behaviour-level, not diff-level.
"The server now …" or "The cart now …", not "Added a check in …".}

**Commits:**
- {sha}: {subject} — failing test
- {sha}: {subject} — fix

### Seam

Where the repair is anchored, in order of durability:

- Test: `{file:line}` — `{behaviour-describing test name}` *(primary seam)*
- Commit: `{sha}` — crack / gold / seam body
- Changelog / doc (if the fix changed a user-visible contract):
  `{file}` — {what was updated}
- Code (only if the fix introduces a value or choice whose rationale
  the code alone can't carry): `{file:line}` — {named constant /
  helper / comment}. Omit this line if no code-level seam was earned.

### Out of scope

Adjacent issues noticed during investigation, *not* fixed in this
pass. Each filed separately if worth pursuing:

- {issue / TODO}: {one-line description + tracking link or decision}

### Verification

- [ ] Failing test written and runs red before fix.
- [ ] Fix lands; failing test runs green.
- [ ] Adjacent tests for the same module still pass.
- [ ] Adjacent-risk scan done (same-shape instances across codebase).
- [ ] Seam left where it won't rot (test name + commit body; changelog
      if contract changed; code-level only if earned).
- [ ] Out-of-scope items recorded separately.
