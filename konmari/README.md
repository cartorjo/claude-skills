# konmari

Marie Kondo-inspired codebase declutter, as a Claude Code skill.

Five categories, one at a time, Keep / Simplify / Release with evidence,
dry-run by default, one commit per category. The workflow is in `SKILL.md`
— this file is the pocket guide for installing, invoking, and resuming.

## Install

### Machine-wide (recommended for cross-repo use)

```bash
cp -r .claude/skills/konmari ~/.claude/skills/
```

Loads in every Claude Code session on this machine. Nothing else to
configure — no `settings.json` edit, no hook, no daemon.

### This repo only (travels with the clone)

The directory at `.claude/skills/konmari/` is already tracked. Contributors
who clone the repo get it automatically.

### Uninstall

```bash
rm -rf ~/.claude/skills/konmari        # machine-wide
rm -rf .claude/skills/konmari          # project
```

## Invoke

Three ways, same skill:

1. **Slash command** (if `.claude/commands/konmari.md` is installed):
   ```
   /konmari
   ```
2. **Explicit ask**:
   > declutter this codebase
   > tidy up / clean up / spring clean
   > konmari the dependencies   (single-category)
3. **Auto-trigger** — the skill's description fires on decluttering intent.

## What happens

1. Skill detects the ecosystem (Liquid, Node, Python, Rust, Go) and loads
   only matching detectors.
2. Produces a dry-run report for **Category 1: Assets & deps**. Three
   buckets — Keep / Simplify / Release — every finding cited.
3. Stops. Asks to apply, skip, or stop.
4. On apply: makes the edits, makes one commit, advances to Category 2.
5. Repeats through Category 5.
6. Final summary mirrors the per-category report shape, aggregated.

The five categories (Kondo's order — do not change):

| # | Kondo | Code |
|---|---|---|
| 1 | Clothes | Dependencies & static assets |
| 2 | Books | Docs & prose |
| 3 | Papers | Config & schemas |
| 4 | Komono | Code & styles |
| 5 | Sentimental | Legacy & compat shims |

## Resume

State is at `.claude/konmari/state.json` (git-ignored). Re-invoking the
skill reads it and offers to continue at the next category.

To start fresh:

```bash
rm -f .claude/konmari/state.json
```

Or ask the skill:

> konmari, restart from scratch

If `.claude/` isn't writable, state falls back to
`$TMPDIR/konmari-<repo-basename>.json` and the skill tells you.

## Evidence discipline

Every finding in a dry-run report carries evidence:

- **Release** — the grep command and its empty result, or a superseding file
  + commit sha.
- **Simplify** — file:line pairs naming the duplicate cluster / dead
  branch / one-line wrapper.
- **Keep** — a one-line "what it earns" note.
- **Kept on purpose** — named items you considered releasing but didn't,
  with the reason.

If something shows up without evidence, it's a bug in the skill (or in a
detector). Flag it, don't silently include it.

## Commit format

```
konmari(<category>): thank & release <N> — <one-line summary>

Released:
- <path>: served for <what>; retired because <why>

Simplified:
- <before> → <after>: <why cleaner>

Kept on purpose:
- <path>: <reason>
```

The "served for / retired because" bit is the Kondo piece. Not
preciousness — readable history. Future you will thank present you.

## Portability checklist

When copying the skill to another machine or another repo, the whole
directory goes together. Everything the skill needs to read lives under
`.claude/skills/konmari/`:

```
SKILL.md
README.md
categories/{1-assets,2-docs,3-config,4-code,5-legacy}.md
detectors/{liquid,node,python,rust,go}.md
templates/{report,commit}.md
state-schema.json
```

No external scripts, no runtime deps, no network calls. Just prose and
grep recipes.

## Extending

To support a new ecosystem:

1. Add `detectors/<ecosystem>.md` with the same shape as the existing
   detector files (see `detectors/node.md` for the canonical example).
2. Add the ecosystem marker to `SKILL.md` step 0 (ecosystem detection).
3. Optionally reference the new detector from per-category playbooks if
   the detectors have category-specific variants.

That's it. Categories and trichotomy rules don't change per ecosystem —
only the detection recipes do.
