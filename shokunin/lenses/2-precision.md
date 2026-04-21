# Lens 2 — Precision

Does the code do what it says, and only that?

Precision is the gap between the identifier on the left and the
behaviour on the right. Wide gap → future bug.

## Checks

- **Name vs body**: does every function / variable / type do *exactly*
  what its name implies? Not less (missing case), not more (hidden
  side effect).
- **Single responsibility per function**: if you read the function and
  need to say "and also", that's a smell.
- **Boundaries honoured**: public functions validate their inputs;
  private functions trust their callers. Not the other way around.
- **Error handling matches the error**: a `NetworkError` caught
  silently under a generic `catch`? A retry loop around a
  non-transient failure?
- **Types tell the truth**: `any`, `unknown`, `interface{}`, `Dict[str,
  Any]` — each occurrence either represents a real unknown or a
  shortcut. Which is it?
- **Off-by-one / boundary**: loops, slice ranges, date comparisons,
  pagination. Shokunin-level attention on the seams.

## Severity guide

**must-change**
- A function does something material its name doesn't imply (e.g.,
  `getUser()` that also writes a log entry, mutates state, or triggers
  a side effect).
- Error handler swallows an error type that should propagate.
- A public function misses input validation where the failure mode is
  a security or data-corruption issue.

**consider**
- Name is accurate but vague (`handle()`, `process()`, `manager`).
- Single function doing two unrelated things that could be split.
- `any` / `unknown` where a specific type would have worked.

**observed**
- Naming convention is slightly off from the rest of the file.
- A boundary check that's technically correct but reads awkwardly.

## Evidence

Cite `file:line`. For name/behaviour mismatches, quote the name and
describe the surprise in one sentence.

## What good looks like

- Reading the function signatures alone gives you an accurate mental
  model of the module.
- Errors fail in the direction of the code that can meaningfully
  respond to them.
- No "clever" — boring, precise, done.
