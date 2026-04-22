# Shokunin review template

Fixed shape: context, lens sections (only where findings exist),
silent-passes line, overall verdict. Every finding carries `file:line`
+ a severity + a one-sentence justification.

---

## Shokunin review — {scope}

**Scope:** {PR #N / `path/to/file.ts:45-120` / "staged diff" / ...}
**Lines changed:** +{added} / -{removed}
**Reviewer:** shokunin skill (taste-level lens, not correctness)

### Context (1 paragraph)

{What this module does, what convention the surrounding code follows,
whether this change is on a hot path / boundary / interior. One
paragraph so the author can confirm we're reviewing the same thing.}

---

### Lens sections

Render a section for each lens that has ≥ 1 finding. Skip lenses with
zero findings — they go on the silent-passes line below. Do not add
"Done well" bullets per lens; craft praise lives once in the overall
verdict, only if earned.

#### Lens N — {Necessity / Precision / Durability / Kindness / Silence}

**Must-change** ({count})
- `file:line` — {finding}. {evidence: what it duplicates, how it
  fails, the shorter version, etc.}

**Consider** ({count})
- `file:line` — {finding}.

**Observed** ({count})
- `file:line` — {finding}.

**Read:** {one-line verdict: "No new entities earn their place" /
"Error handling points the wrong way in two places" / etc.}

---

### Silent passes

{One line listing the lenses that found nothing actionable, e.g.
"No findings: Necessity, Precision, Kindness." Omit this subsection
entirely if every lens had at least one finding.}

---

## Overall verdict

One of:

- **Ship it.** (No must-change findings. Considers and observed are
  optional.)
- **Address the must-change items, then ship.** (Must-change count:
  {N}.)
- **Structural rethink.** (Must-change findings that indicate the
  approach, not the code, needs revision.)

### What the author did well (only if earned)

{2–3 bullets of real craft, each cited to `file:line`. Skip this
subsection entirely if nothing stands out — forced praise is worse
than none.}

### Top three things to change

{If there are must-change or high-confidence consider findings — the
author's reading list in priority order. If none, write "None.".}
