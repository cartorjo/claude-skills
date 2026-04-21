# Category 4 — Komono: Code & styles

Kondo's komono ("miscellany") is the largest category and has the most
subcategories. Code is the same — most of the body of a repo is
functions, components, helpers, and styles, with no single neat
classification. Work by subcategory, one at a time.

## Subcategories

Run in order. Produce one aggregated Category 4 report at the end, with
subsections for each.

1. **Unreachable modules** — files with zero inbound references.
2. **Unused exports** — exported symbols no other file imports.
3. **Duplicated helpers** — two or more functions doing the same thing.
4. **One-line wrappers** — functions that only call another function.
5. **Unused CSS selectors** — selectors no template / component uses.
6. **Commented-out code blocks** — multi-line comments that used to be code.
7. **Debug leftovers** — `console.log`, `debugger`, `print(` debug calls,
   `dbg!`, `dd()` in production paths.
8. **Dead branches** — `if (false)`, `if (FLAG)` where FLAG is always
   one value, unreachable default-case.

## Detectors to run

Load ecosystem detector(s) and run each detector's Category 4 recipes.
The common shape across ecosystems:

- File-level: grep the basename (or the module path) across source; if
  zero inbound hits and the file isn't an entry point / test fixture,
  it's a Release candidate.
- Symbol-level: for each exported symbol, count imports across the
  source tree.
- CSS selectors: intersect selectors in `*.css` with class/id usage in
  `*.{liquid,html,jsx,tsx,vue,svelte}`. Empty intersection = unused.
- Comment blocks: `grep -P '^\s*(//|#)' | uniq -c | sort` to find long
  runs; inspect ones containing punctuation / code syntax.
- Debug: plain grep for `console.log`, `debugger`, etc. in non-test,
  non-script paths.

## Bucket rules

**Keep**
- File / symbol / selector with at least one concrete consumer.
- Required evidence: one-line "what it earns" (what consumes it).

**Simplify**
- Duplicate helper cluster. Evidence: file:line pairs of each duplicate.
  Action: pick the canonical, point others at it.
- One-line wrapper with ≤ 1 consumer. Evidence: the wrapper body + the
  consumer. Action: inline at the callsite, delete the wrapper.
- Dead branch. Evidence: the condition + the flag value.
- Commented-out code block > 3 lines. Action: delete (git history is
  the archive).
- Debug leftover in non-test path. Action: delete.

**Release**
- File with zero inbound references. Evidence: the grep + scope.
- Exported symbol with zero imports + not re-exported. Evidence: the
  grep.
- CSS selector with zero matches in templates. Evidence: the selector +
  the empty template grep.

**Kept on purpose**
- Framework-required entry points (route handlers, plugin lifecycle
  hooks, Liquid section schemas — Shopify loads these by file name and
  grep won't find references).
- Public API surface of a library (consumers are external).
- "Defensive" helpers that guard real edge cases; keep if the edge case
  is documented or demonstrable.

## Evidence format

```
- snippets/old-helper.liquid — released
  grep -rl "'old-helper'\|\"old-helper\"" sections/ snippets/ blocks/ layout/ templates/ → 0 results
- sections/foo.liquid:142 + sections/bar.liquid:87 — simplified
  two near-identical arrow-CTA blocks; extract to snippets/shared-cta.liquid
- assets/events.js:73 — simplified
  stray console.log; delete
```

## Commit subject

```
konmari(code): thank & release <N> — <short summary>
```

## This repo's pre-existing signal

Confirmed during exploration: snippets (103), blocks (98), sections (55),
and JS assets all have zero current orphans — the April 2026 harmonize
pass cleared them. Known Category 4 signal in this repo:

- `assets/events.js` contains a `console.log` — debug leftover, Simplify.
- The CTA extraction in `snippets/gurmeo-text-cta.liquid` (commit
  `6296c4a`) is the template for Simplify "extract duplicate cluster"
  work — match that shape.
