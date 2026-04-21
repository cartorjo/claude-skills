# Lens 4 — Kindness

Is the next reader considered?

Kindness isn't niceness. It's engineering for the person who arrives
at 3am with an incident, no context, and 15 minutes to find the bug.

## Checks

- **Readable error messages**: if this throws, what does the user /
  operator / developer see? "Invalid input" vs "expected
  ISO-8601 date, got '2024-13-40'".
- **Debuggability**: can you tell *why* something failed from the
  stack trace alone, or do you need to re-run with more logging?
- **Discoverability of the right path**: can the next reader find
  this code by grepping for an obvious term? (Unusual abbreviations,
  domain jargon without a glossary.)
- **Named constants over magic numbers**: `TIMEOUT_MS = 500` over `500`.
  Not because the number is wrong — because the name is the
  documentation.
- **Useful commit messages in the diff's commits**: not "fix",
  "wip", "pr feedback". Each commit is a story beat.
- **Test names**: `it('rejects expired tokens')` over `it('test 7')`.
- **Inline comments only where WHY is non-obvious**: not narrating
  WHAT the code does.
- **Co-location of related things**: the thing you'd want to edit
  together should be near each other.

## Severity guide

**must-change**
- Error with no actionable message (or a stack trace that doesn't
  identify the caller).
- Test name that doesn't describe what's being tested.
- Magic number / string in a security-adjacent or data-loss-adjacent
  context.

**consider**
- Commit messages that don't tell the PR's story.
- Abbreviation / jargon without a comment or glossary link.
- Related code split across files unnecessarily.

**observed**
- Constant could be named.
- A test description could be sharper.
- A helper located far from its only caller.

## Evidence

Quote the unkindness verbatim. "This error reads `Error: xxx` — a
3am-you would need to open the file to know what went wrong."

## What good looks like

- The code reads like a short essay: sections, flow, the right
  amount said.
- When it fails, it tells you *why*, not just *that*.
- The next reader's first hour is spent understanding *what to
  change*, not *how the code works*.
