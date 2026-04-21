# Station 3 — CI & PRs

Is anything broken on the remote side that needs attention before new
work?

## Checks

Prefer GitHub MCP tools if available; fall back to `gh` CLI if present;
otherwise skip this station with amber status and a note.

- Open PRs you authored on this repo (status, mergeable, review state).
- CI status on current branch (if pushed).
- Open review comments on your PRs awaiting response.
- Failing checks on your PRs.

## Status rules

🟢 — no open PRs you own blocked on you; current branch's last CI run
green (or branch not pushed yet).

🟡 — any of:
- A PR of yours has review comments waiting.
- CI on current branch is yellow (flaky or in-progress).
- An open PR has merge conflicts.

🔴 — any of:
- CI on current branch is failing.
- A PR of yours is blocking a teammate.
- A review explicitly requested changes on a PR you're leaving stale.

## Notes

If GitHub access is unavailable (no MCP, no `gh`), say so and set
amber. Don't guess.

The goal isn't to triage every notification. It's to catch the "my PR
has been waiting on me for three days and I didn't realize" case
before starting something new.
