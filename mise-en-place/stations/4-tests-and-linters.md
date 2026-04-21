# Station 4 — Tests & linters

Can the feedback loop run at all? Is it fast enough to use?

## Checks

Detect the ecosystem (see konmari/detectors if available) and run one
representative check, not the full suite:

| Ecosystem | Smoke command (example) |
|---|---|
| Node | `npm test -- --listTests 2>&1 \| head` or run one specific test file |
| Python | `pytest --collect-only -q \| head` or `pytest tests/test_smoke.py` |
| Rust | `cargo check --workspace` (not full `cargo test`) |
| Go | `go build ./...` |
| Liquid (Shopify) | `npx @shopify/cli theme check --fail-level error 2>&1 \| tail` |

Linter: run once on a single changed file (or any file if none
changed) to confirm the config loads.

## Status rules

🟢 — smoke command completes in < 10s, exit 0.

🟡 — smoke completes but slowly (> 10s) or with warnings. Next step:
a one-liner pointing at what to profile / silence.

🔴 — smoke fails. Next step: the first error line + the file it points
at. Don't try to fix from this station — the whole point is not to
start new work on top of a broken feedback loop.

## Notes

"Smoke" means "it starts and reports". Full test suites belong in CI,
not in pre-flight.

If the repo has a `make smoke` / `scripts/test-smoke.sh` / equivalent,
prefer that — respect the project's convention.
