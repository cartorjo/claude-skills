# Phase 3 — Show the seam

Kintsugi doesn't hide the repair; it makes the repair itself an
artefact. But the seam has to live somewhere it won't rot. A
`// fixes #4217` comment in the code ages badly — the ticket link
dies, the surrounding code drifts, the comment becomes a lie. Put the
seam where time keeps it honest.

## The seam, in order of durability

### 3.1 The test name (most durable)

The test is the permanent seam. Every CI run re-asserts the bug's
absence. Give the test a name that describes the behaviour being
protected, not the incident:

- Good: `it('preserves author name on blog-comment validation error')`
- Good: `it('handles empty tag array without early-return')`
- Bad: `it('fix for #4217')` — ticket ID rots; reader has no idea
  what's being asserted.

A well-named test *is* the seam. In most bugs, it's the only seam you
need.

### 3.2 The commit body (second-most durable)

Use `templates/commit.md`. The body carries the crack / gold / seam
triad:

- **Crack**: one line — what was broken.
- **Gold**: one or two lines — what the code now does that it didn't.
- **Seam**: one line — where the behaviour is anchored (usually the
  test path + name; sometimes a named helper or constant introduced
  by the fix).

`git log` and `git blame` keep this searchable forever, without any
inline code pollution.

### 3.3 The changelog (if the contract changed)

Update `CHANGELOG.md` / release notes **only** when the fix changed
behaviour a user or integrator would observe. Entry describes the new
contract, not the bug:

- Good: "`parseTags([])` now returns `[]` (previously `null`)."
- Bad: "Fixed bug where empty tag arrays returned null."

Judgement call: did this fix change a contract, or restore the
contract that was already documented?

### 3.4 Code-level seam (only if earned)

Add a named constant, named helper, or inline comment **only** when
the fix introduces a value or choice whose rationale a future reader
couldn't infer from the code itself. Examples where it's earned:

- The fix sets `DNS_LOOKUP_TIMEOUT_MS = 5000` because `2000` caused
  timeouts under a specific network profile. The number itself is
  arbitrary-looking; the name makes it legible.
- The fix introduces `escapeShellArg()` because the bug was missing
  escaping. The helper is now the canonical place, and its name
  documents what it protects against.
- An inline comment that defends a counter-intuitive branch:
  `// empty tags array is valid — do not early-return`. The comment
  explains *why the code is this way*, which the code alone cannot.

Not earned — skip the code-level seam:

- The fix is a one-line correction (typo, flipped operator,
  off-by-one) where the corrected line reads obviously.
- The fix restores a contract the tests already describe. The test
  is the seam.
- A comment that just re-states what the code does, or references a
  ticket without explaining the behaviour.

Rule: a seam comment defends *the behaviour*, not *the change*. If
you can delete the comment and a reader would still understand the
code, delete it.

## Adjacent-risk sweep

Before closing the phase, ask: does the same defect shape exist
elsewhere?

- If the bug was "forgot to escape `form.author`", check the other
  seven form fields. Same-shape instances are in scope for this fix.
- If the bug was "empty-array path returned null", grep for other
  functions with the same signature and check each.

Two possible outcomes:

- **All same-shape instances fixed** — note it in the wrap-up.
- **N other instances noted** — file each as a separate follow-up
  (issue, TODO with tracker, separate kintsugi pass). They do not
  get folded into this fix.

Out of scope: "let me add defensive assertions across the module
just in case." That's plastering, not gold. The seam goes where the
crack was, not across the whole pot.

## Output

- A test with a behaviour-describing name (from phase 2) — the
  primary seam.
- A fix commit using `templates/commit.md` (crack / gold / seam).
- A changelog / doc entry if the contract changed.
- A named constant / helper / comment **only** if the fix introduces
  something whose rationale the code alone can't carry.
- One paragraph on adjacent-risk results.

## Anti-patterns

- **`// fixes #1234` as the seam.** The ticket rots, the comment
  stays, the reader is no better off.
- **Silent fix.** One-line change, no test, no commit-body context.
  Six months later a refactor brings the bug back.
- **Over-seaming.** Assertions sprayed across the module "just in
  case". Scope was the bug, not the module.
- **Ticket-number-only commit.** `fix: #4217` tells the next reader
  nothing; the ticket link will be dead or paywalled by the time they
  need it.
