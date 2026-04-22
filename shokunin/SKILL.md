---
name: shokunin
description: Master-craftsman taste review. Scrutinizes an already-correct change through five lenses — necessity, precision, durability, kindness, silence — for what linters, tests, and ordinary review don't catch. Use when the user invokes `/shokunin`, asks for a "craftsman review", "taste review", "harsh/picky review", or explicitly asks for shokunin feedback. Not a replacement for general code review; run after correctness is established, or as a self-review pass before opening a PR.
---

# shokunin — master-craftsman review

Correctness is table stakes. The shokunin looks at what's left once the
tests pass: does this code *behave well*? Would a craftsman who takes
pride in their work send it out the door?

Five lenses. Apply in order. Each lens produces findings at one of
three severities: **must-change**, **consider**, **observed**.

## Invariants

1. **Correctness is assumed.** Bugs and broken tests aren't in scope —
   this skill reviews the code that works. If correctness is unclear,
   say so and stop; don't dress up a "does it work?" question as taste.
2. **Severity earns its weight.** Every must-change finding has to
   survive the question "would you block the PR on this?" Every consider
   has to survive "would you mention it in review?" Observed is for
   things worth naming but not actioning.
3. **No mixed verdicts.** Each lens ends with a one-line read. If
   you're hedging ("mostly good, but..."), break it into two findings.
4. **The next reader is the audience.** Not the author, not the reviewer.
   "Will someone debugging this at 3am six months from now thank the
   author?" is the durable test.

## Lenses

Apply in order. Later lenses can defer to earlier findings; don't
repeat.

1. `lenses/1-necessity.md` — does this code need to exist?
2. `lenses/2-precision.md` — does it do what it says, and only that?
3. `lenses/3-durability.md` — will this still be right in 6 months?
4. `lenses/4-kindness.md` — is the next reader considered?
5. `lenses/5-silence.md` — no ceremonial code, no over-comments

## Workflow

### 0. Scope

Determine what's being reviewed:

- A PR (diff view, by commit, or squashed)
- A specific file
- A hunk (line range within a file)
- "This change" meaning the working tree's unstaged/staged diff

If ambiguous, ask. Don't review the whole repo unless that's literally
what was asked for.

### 1. Context pass

Before any lens, read enough context to review *this change in situ*,
not in isolation:

- What does this module do? (Read 1–2 adjacent files.)
- What convention does the rest of the code follow? (Naming,
  error-handling shape, test style.)
- Is this code on a hot path, a boundary, or interior?

### 2. Lens pass

Read `lenses/<N>-*.md`. Run its checks on the scoped change. Collect
findings.

### 3. Emit review

Use `templates/review.md`. One section per lens. Each finding cites
`file:line`. End with the overall verdict.

### 4. Silent passes stay silent

If a lens found nothing actionable, don't write a section for it —
list it in the silent-passes line at the end of the review. Don't
force a "done well" bullet per lens; craft praise lives once in the
overall verdict, only if earned.

## When not to use

- On code that doesn't compile or has failing tests — fix correctness first.
- For a trivial change (≤ 10 lines, obvious intent) — shokunin on a
  one-line fix is ceremony, not care.
- When the user asks "does this work?" — that's a correctness review;
  use regular code review, not shokunin.
