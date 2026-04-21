# Station 2 — Branches

Is the working tree clean? Are you on the right branch? Any stale
locals cluttering the list?

## Checks

```bash
git branch --show-current
git status --porcelain        # any uncommitted changes?
git stash list                # any forgotten stashes?
git worktree list             # any stray worktrees?

# Stale locals: merged into main or remote-gone
git branch --merged main | grep -v '^\* \|^  main$\|^  master$'
git fetch -p && git branch -vv | awk '/: gone]/{print $1}'
```

## Status rules

🟢 — on a named feature branch, working tree clean, no stale branches
worth flagging.

🟡 — any of:
- Uncommitted changes that look intentional (WIP).
- Stashes older than 14 days.
- Stale merged branches (> 5, or any older than 30 days).

Next step for 🟡: a one-liner per issue. Don't batch into a "cleanup
branch" pile unless user asks.

🔴 — any of:
- On `main` / `master` with uncommitted work.
- Detached HEAD.
- Uncommitted changes in files you're about to touch for a fresh task
  (they'll collide).

## Notes

"Stale" is fuzzy. Err on the side of surfacing for review rather than
hiding. The user's own worklog / commit cadence is the reference.
