# Per-category dry-run report template

Use this exact shape. Fill in counts and findings; keep empty sections
visible (with a "(none)" marker) so the structure reads the same every
time.

---

## Category {N}: {category-name}

**Scope scanned:** {paths / globs}
**Detectors used:** {detector filenames}

### Keep ({count})

- `{path}` — {one-line: what it earns}

(none) — if no keeps worth naming. Omit only when every file in scope is
uninteresting; otherwise at least name the load-bearing ones.

### Simplify ({count})

- `{path}:{line}` + `{path}:{line}` — {duplicate cluster}; extract to {target}
- `{path}:{line}` — {one-line wrapper}; inline at {callsite}
- `{path}` — {dead branch}; flag `{name}` is {value} across {envs}

(none)

### Release ({count})

- `{path}` — {evidence: grep command → 0 results}
- `{path}` — {evidence: superseded by `{other path}` since commit `{sha}`}

(none)

### Kept on purpose ({count})

- `{path}` — {considered for release; kept because {reason}}

(none) — but this section is rarely empty. If it is, double-check you
actually looked.

### Summary

- Keep: {n}
- Simplify: {n}
- Release: {n}
- Kept on purpose: {n}

---

## Next step

Choose one:

1. **Apply** — I'll make the Simplify + Release edits and open a single
   commit for Category {N}.
2. **Skip** — advance to Category {N+1} without changes.
3. **Stop** — save state and exit.

---

# Final aggregate report template

Used after category 5 (or when the user stops). Same shape, aggregated.

## Konmari pass — summary

**Ecosystem:** {liquid|node|python|rust|go|...}
**Categories processed:** {list}
**Commits:** {shas}

### Totals

| Category | Keep | Simplify | Release | Kept on purpose |
|---|---|---|---|---|
| 1. Assets | n | n | n | n |
| 2. Docs | n | n | n | n |
| 3. Config | n | n | n | n |
| 4. Code | n | n | n | n |
| 5. Legacy | n | n | n | n |
| **Total** | **n** | **n** | **n** | **n** |

### Kept on purpose — durable notes

Everything from each category's "Kept on purpose" section, aggregated.
This is the section future-you will re-read before the next pass.

- `{path}` — {reason}
- ...

### Known remaining

Items discovered but deferred. Match the shape of this repo's
`HARMONIZATION-REPORT.md` "Known Remaining Issues" section:

1. **{short title}** — {1–2 sentences: what, why deferred, what it would take}

### How to resume

State file at `.claude/konmari/state.json`. Re-invoke the skill to
continue. To start fresh, delete the state file or ask the skill to
reset.
