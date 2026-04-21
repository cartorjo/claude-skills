# Shokunin review template

Fixed shape: context, five lens sections, overall verdict. Every
finding carries `file:line` + a severity + a one-sentence justification.

---

## Shokunin review — {scope}

**Scope:** {PR #N / `path/to/file.ts:45-120` / "staged diff" / ...}
**Lines changed:** +{added} / -{removed}
**Reviewer:** shokunin skill (taste-level lens, not correctness)

### Context (1 paragraph)

{What this module does, what convention the surrounding code follows,
whether this change is on a hot path / boundary / interior. This is
for the author to confirm we're reviewing the same thing.}

---

### Lens 1 — Necessity

**Must-change** ({count})
- `file:line` — {finding}. {existing thing it duplicates, with `file:line`}

**Consider** ({count})
- `file:line` — {finding}.

**Observed** ({count})
- `file:line` — {finding}.

**Done well**
- {one thing the author got right under this lens.}

**Read:** {one-line verdict: "No new entities earn their place" / "Two
consumers justify the new abstraction" / etc.}

---

### Lens 2 — Precision

{same shape as Lens 1}

---

### Lens 3 — Durability

{same shape}

---

### Lens 4 — Kindness

{same shape}

---

### Lens 5 — Silence

{same shape}

---

## Overall verdict

One of:

- **Ship it.** (No must-change findings. Considers and observed are
  optional.)
- **Address the must-change items, then ship.** (Must-change count:
  {N}.)
- **Structural rethink.** (Must-change findings that indicate the
  approach, not the code, needs revision.)

### What the author did well

{2–3 bullets of real craft. Not "looks good" — name specifics.}

### Top three things to change

{If any must-change or high-confidence consider findings — the
author's reading list in priority order.}
