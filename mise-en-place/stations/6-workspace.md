# Station 6 — Workspace

Is the local environment actually ready to produce output?

## Checks

| Ecosystem | Check |
|---|---|
| Node | `node --version` matches `.nvmrc` / `package.json#engines`; `node_modules` exists; `package-lock.json` is younger than `package.json` (no drift) |
| Python | virtualenv active (`VIRTUAL_ENV` env var); `pip list` returns; `requirements.txt` / `pyproject.toml` matches installed set |
| Rust | `rustc --version` matches `rust-toolchain.toml`; `target/` exists or `cargo check` passes |
| Go | `go version` matches `go.mod`'s `go` directive; `$GOPATH` sane |
| Liquid (Shopify) | `shopify` CLI installed; `.env` has required store vars |

Env vars: read `.env.example` (or equivalent), check each listed var
is set in the current session.

Editor / IDE hints: if a `.vscode/` or `.idea/` dir exists and has
recommended extensions, surface them. Don't auto-install.

## Status rules

🟢 — runtime version matches, deps are installed, required env vars
are set.

🟡 — any of:
- `node_modules` / `.venv` exists but lock file newer (install drift).
- Optional env vars missing; feature-level, not critical.

🔴 — any of:
- Runtime version wrong (`.nvmrc` vs `node -v` mismatch).
- Required env var absent.
- Lock file missing or radically out of sync.

Next step for 🔴: the single command that fixes it (`nvm use`,
`npm install`, `source .env`). Don't chain multiple.

## "Prep me" mode

If the user explicitly approved "prep me":
- Run `npm install` / `pip install -r ...` / `cargo build` (the
  declared install command only — don't pick).
- Set any env vars from `.env` if the user confirms.
- Re-run this station.
