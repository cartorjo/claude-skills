# Commit message template

One commit per applied category. Subject line:

```
konmari(<category-slug>): thank & release <N> — <one-line summary>
```

`<category-slug>` values (fixed): `assets`, `docs`, `config`, `code`,
`legacy`. `<N>` is the count of released items (not simplified — the
summary in the body covers simplifies separately).

Body:

```
Released:
- <path>: served for <what>; retired because <why>
- <path>: served for <what>; retired because <why>

Simplified:
- <before-path>:<line> + <before-path>:<line> → <after>: <why cleaner>
- <path>:<line>: <change>; <why>

Kept on purpose:
- <path>: <reason>
- <path>: <reason>
```

## Tone

- "Served for / retired because" is the Kondo piece. Keep it literal
  and specific, not sentimental.
- Each bullet is one line. If it wraps, shorten.
- Don't include the grep evidence in the commit — that lived in the
  dry-run report. The commit is the verdict, the report is the trial.

## Example

```
konmari(assets): thank & release 9 — drop unused payment-icon snippets and orphaned dev config

Released:
- snippets/payment-icon-amex.liquid: served for <$currency-amex-block>; retired because gurmeo-payment-icons.liquid inlines all marks as SVG
- snippets/payment-icon-visa.liquid: served for <$currency-visa-block>; retired because gurmeo-payment-icons.liquid inlines all marks as SVG
- ... (6 more payment icons)
- assets/jsconfig.json: served for VS Code IntelliSense during early dev; retired because no longer loaded — theme has no JS build step

Simplified: (none)

Kept on purpose:
- snippets/gurmeo-payment-icons.liquid: canonical icon source, keep
- assets/*-Trial.woff2: still under trial license; rename on license acquisition (deferred to follow-up)
```

## Anti-patterns

- Don't write `konmari: cleanup` — no category, no count, no verdict.
- Don't squash five categories into one commit. One category, one
  commit, one approval. The history reads like the report reads.
- Don't include the skill name in the body. Subject prefix is enough.
- Don't use "removed" / "deleted" language — "released" and "retired"
  match the method's ethos and keep the commit recognisable at a glance.
