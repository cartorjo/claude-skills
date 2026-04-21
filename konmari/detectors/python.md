# Detector: Python

Ecosystem markers: `pyproject.toml`, `setup.py`, `setup.cfg`, or
`requirements*.txt` at repo root.

Source scope:

```
SCOPE="src/ $(jq -r '...package roots...')"   # inferred from pyproject
```

Fallback: any directory containing `__init__.py` at top level.

## Category 1 — Assets & deps

### Unused dependencies

Prefer `deptry`:

```bash
deptry . 2>/dev/null
```

Or `pip-check-reqs`. Fallback grep:

```bash
for dep in $(pip list --format=freeze | cut -d= -f1); do
  hits=$(grep -rl --include='*.py' \
    "^import $dep\|^from $dep " $SCOPE 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE DEP: $dep"
done
```

Note: dep names and import names differ (`Pillow` → `PIL`,
`PyYAML` → `yaml`). Keep a known-alias map or use `deptry` directly.

### Unused static data

Data files under `data/`, `fixtures/`, `static/`, inside packages. Grep
each filename across `.py`, templates, and config.

## Category 2 — Docs

Standard detectors. Additional:

- `Sphinx` docs under `docs/` — outdated API references for removed
  modules are common staleness signal.

## Category 3 — Config

### Unused env vars

```bash
for var in $(grep -E '^[A-Z_]+=' .env.example 2>/dev/null | cut -d= -f1); do
  hits=$(grep -rl --include='*.py' \
    "os\.environ\[.$var.\]\|os\.environ\.get(.$var.\|getenv(.$var" \
    $SCOPE 2>/dev/null | wc -l)
  [ "$hits" = "0" ] && echo "RELEASE ENV: $var"
done
```

### Duplicate config overlaps

- `pyproject.toml` vs `setup.cfg` vs `setup.py` — pick one
- `.flake8` vs `tox.ini` `[flake8]` vs `pyproject.toml` `[tool.flake8]`
- `pytest.ini` vs `pyproject.toml` `[tool.pytest.ini_options]`

## Category 4 — Code & styles

### Unused imports / symbols

Prefer `ruff check --select=F401,F841` (unused imports / vars) or
`vulture`:

```bash
vulture $SCOPE --min-confidence 80 2>/dev/null
```

### Dead files

```bash
for f in $(find $SCOPE -name '*.py' -not -name '__init__.py'); do
  mod=$(basename "$f" .py)
  hits=$(grep -rl --include='*.py' \
    "import $mod\|from .* import $mod\|from \.$mod" $SCOPE 2>/dev/null | \
    grep -v "^$f$" | wc -l)
  [ "$hits" = "0" ] && echo "DEAD FILE: $f"
done
```

Warning: test discovery, entry-points in `pyproject.toml`, Django
`INSTALLED_APPS`, Flask blueprint auto-registration all load modules
without grep-visible imports.

### Stray debug calls

```bash
grep -rnE --include='*.py' 'print\(|breakpoint\(\)|pdb\.set_trace' $SCOPE | \
  grep -v '/tests\?/'
```

### Commented-out code

Same approach as node — inspect grep for comments containing
punctuation / code syntax.

## Category 5 — Legacy

### Python-version shims

Grep for `sys.version_info` branches; any branch for a version below
the declared `python_requires` is Release.

### `six` / `__future__` imports

```bash
grep -rn --include='*.py' 'from __future__\|import six' $SCOPE
```

Python 3-only codebases: `__future__` imports are Release. `six` is a
legacy 2/3 compat lib, typically Release.

### Django / framework deprecations

Check framework version + grep for deprecated-in-newer-versions APIs.
