# Detector: Rust

Ecosystem marker: `Cargo.toml` at repo root (or workspace root).

Source scope: `src/`, plus workspace members listed in root `Cargo.toml`.

## Category 1 — Assets & deps

### Unused dependencies

Prefer `cargo-machete` or `cargo-udeps`:

```bash
cargo machete 2>/dev/null
# or
cargo +nightly udeps 2>/dev/null
```

Fallback grep against `Cargo.toml` `[dependencies]` entries.

### Unused features

Cargo features declared in `Cargo.toml` but never enabled by any
consumer or workspace member are Release.

## Category 2 — Docs

- `src/lib.rs` / `src/main.rs` crate-level docs.
- `README.md` examples that no longer compile — use `cargo test --doc`
  to validate.

## Category 3 — Config

- `rustfmt.toml` vs `.rustfmt.toml` — pick canonical.
- `clippy.toml` — check for rules that lint code no longer present.
- Workspace `[profile.*]` overrides not used by any target.

## Category 4 — Code & styles

### Unused symbols

`cargo clippy` with `-W dead_code -W unused` does most of this. Run
once, let clippy report.

```bash
cargo clippy --workspace --all-targets --message-format=short 2>&1 | \
  grep -E 'dead_code|unused'
```

### Dead modules

Modules declared with `mod foo;` but nothing inside the crate uses
`foo::`. Clippy catches many; grep backs it up.

### Stray debug

```bash
grep -rnE --include='*.rs' 'dbg!|println!\(|eprintln!\(' src/
```

Context matters here — `println!` in a binary is often intentional.

## Category 5 — Legacy

### Edition shims

Check `edition` in `Cargo.toml`. Code guarded by `#[cfg(rust_2018_idioms)]`
or similar on a 2021+ edition crate is Release.

### `extern crate` declarations

On 2018+ edition, `extern crate foo;` is legacy (the `use` statements do
the job). Grep for them.

### MSRV-driven shims

If `rust-version` in `Cargo.toml` has been bumped, shims for older
versions are Release candidates.
