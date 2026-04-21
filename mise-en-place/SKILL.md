---
name: mise-en-place
description: Pre-flight prep ritual before starting a coding session. Walks six kitchen-style stations (sync, branches, CI/PRs, tests, context, workspace) and flags anything that needs attention before the first edit. Use when the user says "let's start", "what should I work on", "am I ready to code", "session prep", or types `/mise-en-place`. Also run on session resume if the state is ambiguous.
---

# mise-en-place — pre-flight prep

Kitchen rule: everything in its place before you cook. Six stations, each
a quick check. The point isn't thoroughness — it's catching the one
thing that would have wasted the next hour.

Non-mutating by default. Reports; doesn't fix. Unless the user says
"prep me", in which case make the obvious fixes (pull, install, set
env) and re-report.

## Invariants

1. **Stations in order.** You can skip one, but you can't reorder. Order
   is dependency-aware: later stations assume earlier ones pass.
2. **Each station is one screen.** If a station's report runs to more
   than a dozen lines, something is wrong — split the finding out as a
   separate concern the user should handle before continuing.
3. **Red / amber / green, nothing else.** Each station ends with one
   status. If it's amber or red, say exactly what to do about it.
4. **Non-destructive unless asked.** Don't pull, don't install, don't
   run migrations without explicit approval. "Prep me" = approval.

## Stations

Run in order, stopping only if a station is red and would corrupt the
next one.

1. `stations/1-sync.md` — fetch, check upstream drift on current branch
2. `stations/2-branches.md` — stale locals, worktree cleanliness, uncommitted diff
3. `stations/3-ci-and-prs.md` — open PRs you own, CI status, review blockers
4. `stations/4-tests-and-linters.md` — can they run? one fast smoke test
5. `stations/5-context.md` — recent commits, WORKLOG-style notes, open TODOs
6. `stations/6-workspace.md` — deps installed, env loaded, IDE ready

## Workflow

1. Detect the repo and runtime (reuse konmari's ecosystem detection
   signals if konmari is also installed: `package.json`, `*.liquid`,
   `pyproject.toml`, `Cargo.toml`, `go.mod`).
2. Run each station. Collect the status.
3. Emit the report using `templates/report.md`.
4. If any station is red, stop at the report. Don't continue into
   actual work.
5. If the user says "prep me" (or equivalent), make the obvious fixes
   and re-run the affected station only.

## Output shape

See `templates/report.md`. Six sections, each ≤ 12 lines, each ending
with 🟢 / 🟡 / 🔴 and a next-step one-liner.

## When not to run

- Middle of a session you already opened. Mise-en-place is a start-of-session ritual, not a middle-of-work interrupt.
- On a one-line fix where the prep overhead exceeds the work.
- When the user explicitly says "just do X" — they're asking for
  output, not ceremony.
