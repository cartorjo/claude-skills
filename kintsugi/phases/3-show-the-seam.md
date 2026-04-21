# Phase 3 — Show the seam

Leave the repair visible. Future you, next on-call, new hire six
months from now — they should see where the crack was and why it's
fixed this way. Kintsugi doesn't hide; it highlights.

## Steps

### 3.1 The seam in the code

At the fix site, leave a marker the next reader will find:

- A named constant over a magic number. If the bug was "timeout was
  too short", introduce `DNS_LOOKUP_TIMEOUT_MS = 5000` rather than
  changing `2000` to `5000` inline.
- A named helper. If the bug was "forgot to escape this string",
  the fix uses a helper named for what it protects against:
  `escapeShellArg()`, not inline `replace(/"/g, '\\"')`.
- A narrow, specific comment. Not "fix bug #4217" — "empty tag array
  is valid (see issue #4217); do not early-return". The comment
  defends *the behaviour*, not the change.

Rule of thumb: the seam answers "why is this line the way it is?" not
"what does this line do?"

### 3.2 The seam in the commit

Use the commit template (`templates/commit.md`). The body carries:

- **Crack**: one line. What was broken.
- **Gold**: one line. What the fix changes and why.
- **Seam**: one line. Where to look if this ever resurfaces.

### 3.3 The seam in the test

The test is the durable seam. Give it a name that would ring a bell
if a future refactorer saw the test fail. `it('handles empty tag
arrays without early-return')` sticks; `it('fix for #4217')` rots.

### 3.4 The seam in the docs (if warranted)

For bugs that changed behaviour a user or integrator would notice:

- Update the relevant doc / README / changelog entry.
- Note the behaviour change explicitly. "Previously returned null on
  empty input; now returns an empty array."

Not every bug warrants a doc change. Judgment call: did this fix
change a contract, or restore it?

### 3.5 The defensive layer (optional)

If the bug class suggests a systemic weakness — a whole category of
inputs that could trigger similar failures — consider a defensive
assertion at the boundary. *Not* a catch-all try/catch; a specific
assertion that fails loudly.

This is the part of kintsugi most easily overdone. Keep it narrow.

## Output

- Named constants / helpers / comments at the fix site.
- A commit message that tells the crack/gold/seam story.
- A test named for behaviour, not the bug.
- Doc updates if the bug changed a contract.

## Anti-patterns

- **Silent fixes**: the bug was subtle, the fix is one line, no
  comment, no test, no trace. Six months later, someone refactors and
  the bug comes back.
- **Decorative fixes**: a `// fixes #4217` comment with no explanation
  of what the code is doing differently. The ticket number ages;
  the prose saves.
- **Over-seaming**: wrapping the whole module in defensive assertions
  "just in case". The seam should be where the gold poured, not
  plastered across the pot.

## The durable artefact

Phase 3 is where the bug becomes a story the codebase tells. Done
right, the next reader hits the named constant / comment / helper
and understands the bug's existence and its fix without needing to
pull up the ticket. That is kintsugi.
