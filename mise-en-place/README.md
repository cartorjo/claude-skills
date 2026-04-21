# mise-en-place

Pre-flight prep ritual before a coding session. Six kitchen-style
stations, each a one-screen check. The point is to catch the one thing
that would have wasted the next hour.

## Install

```bash
cp -r mise-en-place ~/.claude/skills/
```

## Invoke

- `/mise-en-place` (slash command) — if you've dropped the optional
  dispatcher at `.claude/commands/mise-en-place.md`.
- "let's start" / "session prep" / "am I ready to code" — skill
  auto-triggers on these.

## Stations

1. **Sync** — fetch, upstream drift on current branch
2. **Branches** — stale locals, dirty worktree
3. **CI & PRs** — open PRs you own, CI status, review blockers
4. **Tests & linters** — can they run? one fast smoke
5. **Context** — recent commits, worklog notes, open TODOs
6. **Workspace** — deps installed, env loaded

## Output

Each station: ≤ 12 lines, one of 🟢 / 🟡 / 🔴, one next-step line.
Full shape in `templates/report.md`.

## Modes

- **Default**: report only. Non-destructive. Fast.
- **Prep me**: on approval, make the obvious fixes (pull, install, set
  env) and re-run the affected station.
