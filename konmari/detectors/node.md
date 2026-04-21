# Detector: Node / JavaScript / TypeScript

Ecosystem marker: `package.json` at repo root.

Secondary markers refine detection:
- `tsconfig.json` → TypeScript
- `next.config.*` / `nuxt.config.*` / `vite.config.*` / `webpack.config.*`
  → bundled app
- `*.test.ts` / `__tests__/` → test scope

Source scope:

```
SCOPE="src app lib components pages api"   # whichever exist
SCOPE=$(echo $SCOPE | tr ' ' '\n' | while read d; do [ -d "$d" ] && echo "$d"; done)
```

## Category 1 — Assets & deps

### Unused dependencies

Prefer `depcheck` if installed; fall back to grep:

```bash
npx depcheck --json 2>/dev/null || true
```

Without `depcheck`:

```bash
for dep in $(jq -r '.dependencies // {} | keys[]' package.json); do
  hits=$(grep -rl --include='*.{js,jsx,ts,tsx,mjs,cjs}' \
    "from ['\"]$dep\|require(['\"]$dep" $SCOPE 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE DEP: $dep"
done
```

Repeat for `devDependencies`, but match in test + config + tooling
scope too (config files often import dev tooling without being in `src/`).

### Unused static assets

```bash
for a in $(find public/ static/ assets/ -type f 2>/dev/null); do
  base=$(basename "$a")
  hits=$(grep -rl --include='*.{js,jsx,ts,tsx,css,html,md}' \
    "$base" $SCOPE 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE: $a"
done
```

### Bundle-size check

Read `packageManager` + any `size-limit`, `bundlewatch`, or
`performance.budgets` config. Compare latest build output to budget.

## Category 2 — Docs

Standard docs scope. Additionally check:

- `JSDoc` / `TSDoc` comments referencing symbols that no longer exist
  (the symbol was renamed or removed; the doc stuck around).

## Category 3 — Config

### Duplicate tool configs

Common overlaps — pick the canonical, release the other:

- `.prettierrc` vs `.prettierrc.json` vs `prettier.config.js` vs
  `prettier` key in `package.json`
- `.eslintrc` vs `.eslintrc.json` vs `.eslintrc.js` vs `eslint.config.js`
  vs `eslintConfig` in `package.json`
- `babel.config.js` vs `.babelrc` vs `.babelrc.json`

### Unused env vars

```bash
for var in $(grep -E '^[A-Z_]+=' .env.example | cut -d= -f1); do
  hits=$(grep -rl --include='*.{js,jsx,ts,tsx,mjs}' \
    "process\.env\.$var\|import\.meta\.env\.$var" $SCOPE 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE ENV: $var"
done
```

### Dead CI jobs

```bash
ls .github/workflows/ 2>/dev/null | head
```

Check each for triggers that never fire.

## Category 4 — Code & styles

### Unused exports (requires tsc / knip / ts-unused-exports)

If `knip` is installed:

```bash
npx knip --reporter json 2>/dev/null || true
```

Fall back to `ts-unused-exports` or manual import-count check per
exported symbol.

### Dead files

```bash
for f in $(find $SCOPE -name '*.ts' -o -name '*.tsx' -o -name '*.js' -o -name '*.jsx'); do
  [[ "$f" == *.test.* || "$f" == *.spec.* ]] && continue
  base=$(basename "$f" | sed -E 's/\.(ts|tsx|js|jsx|mjs|cjs)$//')
  hits=$(grep -rl --include='*.{js,jsx,ts,tsx,mjs,cjs}' \
    "from ['\"].*/$base['\"']\|from ['\"]\./$base['\"']\|require(['\"].*/$base" \
    $SCOPE 2>/dev/null | grep -v "^$f$" | wc -l)
  [ "$hits" = "0" ] && echo "DEAD FILE: $f"
done
```

Warning: framework-routed files (`pages/`, `app/`, `src/routes/`) are
loaded by convention; grep won't find references. Add those directories
to the skip list before running.

### Stray debug calls

```bash
grep -rn --include='*.{js,jsx,ts,tsx}' \
  'console\.log\|console\.debug\|debugger' $SCOPE | \
  grep -v '\.test\.\|\.spec\.'
```

### Commented-out code blocks

```bash
grep -rnE --include='*.{js,jsx,ts,tsx}' '^\s*//.*[{};=]' $SCOPE | head -30
```

## Category 5 — Legacy

### Polyfills vs Browserslist

Read `browserslist` from `package.json` or `.browserslistrc`. Any
`core-js` / `regenerator-runtime` / IE-era polyfill dep that doesn't
match the targets is Release.

### Feature-flag branches

If the repo uses a flag lib (LaunchDarkly, Unleash, Statsig), list flag
usages and cross-check with flag config files / dashboard exports.

### Deprecated aliases

```bash
grep -rnE --include='*.{ts,tsx}' 'export \{.*as.*\}' $SCOPE | head -20
```

Match aliases against import sites; unused aliases are Release.
