# shokunin

Master-craftsman code review. Five lenses beyond correctness.

## Install

```bash
cp -r shokunin ~/.claude/skills/
```

## Invoke

- `/shokunin` on a PR, file, or hunk.
- "review this like a craftsman" / "harsh review please" — skill
  auto-triggers.
- Use as a self-review pass before opening a PR.

## The five lenses

1. **Necessity** — does this code need to exist?
2. **Precision** — does it do what it says, and only that?
3. **Durability** — will this still be right in 6 months?
4. **Kindness** — is the next reader considered?
5. **Silence** — no ceremonial code, no over-comments

## Output

Per-lens findings at three severities:

- **must-change** — would block the PR
- **consider** — worth mentioning in review
- **observed** — worth naming, not actioning

Lenses with zero findings collapse to a one-line silent-passes
mention — no filler "done well" bullets forced per lens.

Full shape in `templates/review.md`.

## Pairs with

- Run **before** opening the PR: self-shokunin catches the obvious
  stuff so external review can focus on the subtle.
- Run **after** konmari to confirm Simplify decisions actually
  simplified.
