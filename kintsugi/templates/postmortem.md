# Kintsugi postmortem template

For incidents — anything that paged someone, broke a customer
workflow, caused data loss or silent corruption, or required a
rollback. Fix-only bugs use `bug-report.md`; this template is tuned
for the write-up that goes in the incident log.

The core difference: a bug report documents the repair. A postmortem
documents the conditions that allowed the crack **and** the repair.
The crack is one entry in a longer story about detection, impact,
contributing factors, and latent risk.

## Blameless framing

Describe the system's behaviour, not a person's. Conditions caused
the incident; people are evidence of conditions.

- Good: "The on-call hadn't seen this alert path before; the runbook
  didn't cover it."
- Bad: "Alice didn't know how to respond to the alert."

The goal isn't politeness — it's that "Alice didn't know" leads
nowhere actionable. "The runbook didn't cover it" produces a concrete
follow-up.

---

## {Short title — what broke, observably}

### Summary (2–3 sentences)

{What happened, who was affected, how it was resolved. Written for
someone skimming the incident log a year from now.}

### Timeline

Facts only, no interpretation. UTC if multi-timezone.

- `HH:MM` — {event: trigger / alert / escalation / action / resolution}
- `HH:MM` — …

### Impact

- **Scope:** {users / regions / systems affected}
- **Duration:** {from first-impact to fully-resolved}
- **Severity:** {data loss? revenue? SLA breach? user-facing error?
  silent corruption?}

### Detection

How we learned about it, and what could have caught it sooner.

- **Signal that fired:** {alert / ticket / customer report /
  discovered in passing}
- **Time from cause to detection:** {minutes / hours / days}
- **Missed signals:** {alerts that should have fired but didn't;
  dashboards that should have been red but were green; logs nobody
  was looking at}

### Crack

(The kintsugi crack shape, extended for incidents.)

- **Symptom:** {what the user / operator observed}
- **Trigger:** {the input / sequence / state that exercised the
  defect}
- **Contributing factors:** {system-level conditions that let the
  trigger cause the incident — e.g., "the monitor was pointing at
  staging, not prod"; "retry logic amplified a transient failure
  into an outage"; "the deploy happened during on-call handover"}
- **Class:** {off-by-one / race / silent-corruption / capacity /
  config-drift / dependency-failure / …}

### Gold

{1–3 sentences describing the fix. Behaviour-level. If a hotfix
preceded a proper fix, describe both and mark which is which.}

**Commits:**
- `{sha}` — hotfix: {subject}
- `{sha}` — proper fix: {subject}
- `{sha}` — rollback (if applicable): {subject}

### Where we got lucky

Latent conditions that didn't cause harm this time but could have.
Each entry is a follow-up candidate.

- {the condition} — {why it didn't bite now; what could make it
  harmful next time}

If nothing here, say so explicitly — "no latent risk surfaced" — so
the reader knows the section was considered, not skipped.

### Seam

Where the repair is anchored, in order of durability:

- **Test:** `{file:line}` — `{behaviour-describing test name}`
  *(primary seam)*
- **Commit:** `{sha}` — crack / gold / seam body
- **Monitoring:** `{alert / dashboard / runbook}` — {what was added
  or changed so this detects sooner next time}
- **Changelog / doc:** `{file}` — {if the contract or runbook
  changed}
- **Code (only if earned):** `{file:line}` — {named constant /
  helper / comment whose rationale the code alone can't carry}

### Action items

Each with an owner and a tracker. No "we should think about X" items
— those are reading lists, not action items.

- [ ] `{owner}` — {action} — `{tracker / issue}`
- [ ] …

### What went well

Real. Name specifics. If nothing stands out, skip the section.

### What went poorly

Conditions, not people. Link to action items above.

### Out of scope

Adjacent issues surfaced during investigation, filed separately:

- `{issue / TODO}`: {one-line description + tracker}

---

## Verification checklist

- [ ] Timeline in UTC, facts only.
- [ ] Impact quantified (scope × duration × severity).
- [ ] Detection section names what fired *and* what should have.
- [ ] Contributing factors are system-level, not individual-level.
- [ ] "Where we got lucky" considered — explicitly empty or filled.
- [ ] Action items have owners and trackers.
- [ ] Language is blameless throughout.
