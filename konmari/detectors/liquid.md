# Detector: Shopify / Liquid themes

Ecosystem markers that select this detector:
`*.liquid` files + `sections/`, `snippets/`, `blocks/`, `layout/`,
`templates/` directory structure. `.theme-check.yml` is a strong
confirmatory signal.

Source scope used by the recipes below:

```
SCOPE="sections snippets blocks layout templates config"
```

Asset scope:

```
ASSETS="assets"
```

## Category 1 — Assets & deps

### Unreferenced snippets

A snippet is referenced via `{% render 'name' %}` or
`{% include 'name' %}` (deprecated but still seen).

```bash
for f in snippets/*.liquid; do
  name=$(basename "$f" .liquid)
  hits=$(grep -rl --include='*.liquid' --include='*.json' \
    "render '$name'\|render \"$name\"\|include '$name'\|include \"$name\"" \
    $SCOPE 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE: $f"
done
```

### Unreferenced asset files (JS / CSS / fonts / images)

Assets are referenced by `asset_url` / `asset_img_url` filters, by
`{{ '<name>' | asset_url }}`, or by string match in templates.

```bash
for a in assets/*; do
  name=$(basename "$a")
  # Skip generated bundles that framework loads by convention
  [[ "$name" == "base.css" || "$name" == "theme.js" ]] && continue
  hits=$(grep -rl --include='*.liquid' --include='*.json' --include='*.js' \
    "$name" $SCOPE assets/ 2>/dev/null | \
    grep -v "^assets/$name$" | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE: $a"
done
```

### Unreferenced blocks / sections

Blocks are registered in `blocks:` arrays inside section schemas, or
rendered via `{% content_for 'blocks' %}`. Sections are rendered via
template JSON + `{% section %}`.

```bash
# Sections
for f in sections/*.liquid; do
  name=$(basename "$f" .liquid)
  hits=$(grep -rl --include='*.json' --include='*.liquid' \
    "\"type\": *\"$name\"\|section '$name'\|section \"$name\"" \
    templates/ config/ sections/ 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE: $f"
done

# Blocks (files starting with _ are private-to-section; bare names are theme blocks)
for f in blocks/*.liquid; do
  name=$(basename "$f" .liquid)
  hits=$(grep -rl --include='*.json' --include='*.liquid' \
    "\"type\": *\"$name\"" templates/ config/ sections/ blocks/ 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE: $f"
done
```

### Trial / placeholder font files

```bash
ls assets/ | grep -iE 'trial|placeholder|sample|demo|-dev\.|-test\.'
```

Simplify action: rename to production filenames on license acquisition;
update `@font-face` references simultaneously.

### Asset size budgets

Read thresholds from `.theme-check.yml`:

```bash
grep -A1 'AssetSizeCSS\|AssetSizeJavaScript' .theme-check.yml
```

Findings over threshold go to Simplify with evidence = actual size +
budget.

## Category 2 — Docs

Liquid themes typically have:
- `README.md` (often upstream Shopify boilerplate — check if customised)
- `release-notes.md`
- `docs/`

Use common detectors from `categories/2-docs.md`. The Shopify CLI docs
at `docs/` may be pristine boilerplate — if last-touched matches the
fork date and content matches upstream, flag for Release with evidence
= diff against `Shopify/horizon@main:docs/`.

## Category 3 — Config

### Locale keys never used

```bash
for key in $(jq -r 'paths(scalars) | join(".")' locales/en.default.json); do
  hits=$(grep -rl --include='*.liquid' "'$key'\|\"$key\"\|t: $key" $SCOPE 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE LOCALE KEY: $key"
done
```

(Warning: slow on large locale files. Sample first; run full only on
approval.)

### `settings_schema.json` sections with no active usage

A settings group with no matching `settings.<key>` reference in source
is Release.

### CI workflow audit

Check `.github/workflows/*.yml` for jobs that reference deleted paths
(`paths:` globs matching nothing).

## Category 4 — Code & styles

### Commented-out Liquid blocks

```bash
grep -rn --include='*.liquid' -B0 -A0 '{% comment %}' $SCOPE | head -50
```

Manually inspect blocks > 3 lines.

### Unused CSS selectors

```bash
# Class selectors in CSS
grep -ohE '\.[a-zA-Z_][a-zA-Z0-9_-]*' assets/*.css | sort -u > /tmp/css-classes
# Class references in templates
grep -rohE 'class="[^"]*"' --include='*.liquid' $SCOPE | \
  grep -ohE '[a-zA-Z_][a-zA-Z0-9_-]*' | sort -u > /tmp/tmpl-classes
# Unused = in CSS but not in templates
comm -23 /tmp/css-classes /tmp/tmpl-classes | head -50
```

Warning: class names may be constructed dynamically (`class="card-{{
variant }}"`). Treat findings as candidates, not conclusions.

### Stray debug calls

```bash
grep -rn --include='*.js' 'console\.log\|console\.debug\|debugger' $ASSETS
```

## Category 5 — Legacy

### Deprecated Liquid filters

```bash
grep -rn --include='*.liquid' 'img_url\|include ' $SCOPE | head
```

`img_url` → `image_url`, `{% include %}` → `{% render %}`. Any hit is
Release / Simplify.

### Typekit or other removed remote assets

Check `.theme-check.yml` RemoteAsset allowlist vs. actual usage. This
repo's known remaining issue (`HARMONIZATION-REPORT.md` #3) lives here.
