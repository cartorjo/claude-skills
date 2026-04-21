# Pre-flight report template

Fixed six sections, each ≤ 12 lines, each ending with one status
indicator and a one-line next step. No more, no less.

---

## Mise-en-place — {repo-basename}

**Branch:** `{branch}`  •  **Ecosystem:** {detected}  •  **Date:** {ISO}

### 1. Sync  {🟢/🟡/🔴}
{2-4 lines: current branch, ahead/behind, key divergence}
→ {one-line next step, or "continue"}

### 2. Branches  {🟢/🟡/🔴}
{2-4 lines: working tree state, stale locals count, stash count}
→ {one-line next step, or "continue"}

### 3. CI & PRs  {🟢/🟡/🔴}
{2-4 lines: open PRs you own, CI status, blockers}
→ {one-line next step, or "continue"}

### 4. Tests & linters  {🟢/🟡/🔴}
{2-4 lines: smoke command + result}
→ {one-line next step, or "continue"}

### 5. Context  {🟢/🟡/🔴}
{one-paragraph summary of recent work + open threads}
→ {suggested focus for this session, or "user to confirm"}

### 6. Workspace  {🟢/🟡/🔴}
{2-4 lines: runtime version, deps status, env vars}
→ {one-line next step, or "ready"}

---

### Overall: {🟢/🟡/🔴}

{If all green: "Station's set. Ready to cook."}
{If any red: "Stop. Resolve {station-N} before starting new work."}
{If yellow-only: "Workable. Heads up: {one-line per amber}."}

---

### "Prep me" offer

If any station is fixable with a single command, offer it:

> Say "prep me" and I'll: {concrete list of commands, max 3}.

Don't offer prep for red-status items that need user judgment
(merge conflicts, stash triage, unclear context).
