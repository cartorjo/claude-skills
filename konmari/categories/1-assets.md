# Category 1 — Clothes: Dependencies & static assets

Kondo starts with clothes because you already know what fits. Assets and
deps are the code equivalent: the binary call of "do we use this?" is
answerable with grep, no taste required. This category builds momentum.

## Scope

- Declared dependencies (manifest files: `package.json`, `pyproject.toml`,
  `Cargo.toml`, `go.mod`, `Gemfile`, etc.)
- Static files under `assets/`, `public/`, `static/`, `static_root/`,
  `vendor/`, `third_party/`
- Fonts, images, icons, vendored libraries
- Asset bundles vs. declared budgets (theme-check / webpack / vite config)

## Detectors to run

Load the ecosystem detector(s) matching the repo and run its
**Category 1** recipes. Common to all:

- Orphaned asset files (zero inbound references across repo)
- Trial / placeholder / demo filenames (`*-Trial*`, `*-demo*`, `*-sample*`,
  `*_TODO*`)
- Duplicate assets (same basename under different paths)
- Bundles over declared budget

## Bucket rules

**Keep**
- Asset referenced directly in templates / components / styles.
- Or linked from a manifest / sitemap / well-known path.

**Simplify**
- Trial or placeholder filenames currently in production use. Evidence:
  filename + the manifest entry referencing it. Action: rename to
  production filename on license/asset acquisition.
- Near-duplicate assets. Evidence: file paths + checksum comparison.
  Action: keep one canonical copy, point others at it.
- Bundles that exceed budget and have a known slimming path (unused
  exports, tree-shakeable import). Evidence: bundle size + budget from
  config.

**Release**
- Asset file with zero inbound references. Evidence: the grep command
  across `sections/ snippets/ blocks/ layout/ templates/ config/ assets/`
  (or ecosystem equivalents) returning empty.
- Declared dependency with zero import / require sites. Evidence: the
  dep-name search across source tree returning empty.
- Vendored library superseded by a first-party alternative or upstream
  package. Evidence: the replacement + commit sha.

**Kept on purpose** (examples of things you'll see and must not release)
- Files referenced only in CI / scripts / docs — search those paths too
  before concluding "unused".
- Assets loaded by string concatenation (`assets/icon-${name}.svg`).
  If you can't prove zero references, keep.
- Assets required by external services (robots.txt, sitemap.xml,
  favicon.ico, .well-known/*).

## Evidence format

Per finding:

```
- <path> — released
  grep -r --include='*.liquid' --include='*.json' '<basename>' \
    sections/ snippets/ blocks/ layout/ templates/ config/ → 0 results
```

## Commit subject

```
konmari(assets): thank & release <N> — <short summary>
```

Example summaries: "drop 8 unused payment-icon snippets",
"remove orphaned dev config", "rename trial fonts to production names".

## This repo's pre-existing signal

`HARMONIZATION-REPORT.md` already documents Category 1 work: 8 unused
payment-icon snippets + `assets/jsconfig.json` were released in commit
`5913520`. Known remaining issue #2 (trial font filenames) is this
category's current Simplify entry.
