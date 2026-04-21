# Station 1 — Sync

Is your local view of the world current?

## Checks

```bash
git fetch --all --prune
git status -sb       # current branch + ahead/behind
git log --oneline origin/HEAD..HEAD    # what you have that main doesn't
git log --oneline HEAD..origin/HEAD    # what main has that you don't
```

Plus: if this repo has an `upstream` remote (fork), show divergence
from upstream too.

## Status rules

🟢 — local branch in sync with its tracking branch, or cleanly ahead by
your own unpushed commits.

🟡 — behind the tracking branch (pullable, no conflict). Next step:
`git pull --ff-only`.

🔴 — diverged (commits on both sides). Next step: user decides rebase
vs merge; don't auto-resolve.

Or 🔴 — current branch has no upstream and isn't named like a throwaway.
Next step: set upstream with `git push -u origin <branch>`.

## Notes

Don't pull unless the user says "prep me". Showing the command is the
deliverable; running it is a separate ask.
