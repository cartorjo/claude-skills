# Detector: Go

Ecosystem marker: `go.mod` at repo root.

Source scope: the module root + all internal packages listed by
`go list ./...`.

## Category 1 — Assets & deps

### Unused dependencies

`go mod tidy` is authoritative — if it removes something, it was unused.
For dry-run inspection:

```bash
go mod tidy -v 2>&1 | grep 'unused\|removing'
```

Or `go-mod-outdated` / `depth` for import-graph analysis.

### Unused static assets

`embed.FS` file references are discoverable via `//go:embed` directives.
Anything under `embed.FS`-referenced directories but not matched by the
directive pattern is Release.

## Category 2 — Docs

Standard detectors. Go-specific:

- `doc.go` files whose package-level comment doesn't match the actual
  package contents.
- Stale `Example*` functions that reference removed APIs.

## Category 3 — Config

- Tool configs: `.golangci.yml`, `.goreleaser.yml` — check referenced
  tools / build targets still exist.
- `tools.go` pattern — unused tool imports.

## Category 4 — Code & styles

### Unused symbols

`staticcheck` + `unused` (bundled with staticcheck):

```bash
staticcheck -checks U1000 ./... 2>/dev/null
```

`U1000` = unused identifiers (types, functions, vars, consts, fields).

### Dead files

```bash
go list -json ./... 2>/dev/null | \
  jq -r 'select(.GoFiles) | .Dir + "/" + .GoFiles[]' | sort -u > /tmp/go-live
find . -name '*.go' -not -path './vendor/*' | sort -u > /tmp/go-all
comm -23 /tmp/go-all /tmp/go-live
```

### Stray debug

```bash
grep -rnE --include='*.go' 'fmt\.Println|fmt\.Printf|log\.Println' $(go list -f '{{.Dir}}' ./...)
```

Context: structured logging (`slog`, `zap`, `zerolog`) is intentional;
`fmt.Println` in non-main packages is usually the stray.

## Category 5 — Legacy

### Go-version shims

Check `go` directive in `go.mod`. Build tags for older versions
(`//go:build go1.17`) below the declared version are Release.

### Vendor directory staleness

If `vendor/` exists, `go mod vendor` should be idempotent. Drift =
Simplify.

### Deprecated stdlib use

`go vet` + `staticcheck SA1019` reports deprecated API usage.

```bash
staticcheck -checks SA1019 ./... 2>/dev/null
```
