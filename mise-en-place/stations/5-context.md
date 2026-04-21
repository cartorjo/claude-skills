# Station 5 — Context

What was the last session about? What was left unfinished?

## Checks

```bash
git log --oneline -20              # recent commit cadence and themes
git log --since='7 days ago' --oneline
```

Look for and read, if present:
- `.claude/WORKLOG.md`, `.claude/NOTES.md`, or similar session log
- `TODO.md`, `NEXT.md` at repo root
- Any file modified but uncommitted that looks like a note-to-self
  (start with `wip`, `draft`, contain `TODO:` at the top)
- Open issues / drafts in PR queue (if station 3 had access)

Scan for `TODO/FIXME/XXX/HACK` comments added in the last 30 days (git
blame), not the whole-history backlog.

## Status rules

🟢 — recent activity is coherent, a clear candidate next task is
visible (either a WIP branch, a worklog entry, or a "next step" in a
PR description).

🟡 — ambiguous state: multiple WIP threads, or no worklog but many
recent commits across different areas. Next step: ask the user what
they want to work on; don't guess.

🔴 — uncommitted files older than 7 days look abandoned. Next step:
either finish, stash, or commit-WIP before starting new work.

## Output

Include in the report a one-paragraph "**Context:**" summary: what the
last few days of work were about, what seems open. The user reads this
to confirm they and the skill are on the same page before work starts.
