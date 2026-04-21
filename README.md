# claude-skills

A collection of [Claude Code](https://docs.claude.com/en/docs/claude-code)
skills for software work. Each one takes a named method from outside
software — Marie Kondo, a Japanese kitchen, a master craftsman, a
lacquer-repair tradition — and uses the metaphor to produce real
structure: categories, lenses, phases, rituals.

## The skills

| Skill | Method | What it does |
|---|---|---|
| [`konmari`](./konmari) | Marie Kondo | Codebase declutter. Five categories, Keep/Simplify/Release with evidence, one commit per category. |
| [`mise-en-place`](./mise-en-place) | French kitchen prep | Pre-flight ritual before coding. Six stations, each a one-screen check. |
| [`shokunin`](./shokunin) | Japanese master-craftsman | Code review through five lenses — necessity, precision, durability, kindness, silence. |
| [`kintsugi`](./kintsugi) | Japanese gold-joinery | Bug hygiene. Three phases: name the crack, pour the gold, show the seam. |

Each skill has its own `README.md` with install + invocation notes, a
`SKILL.md` with the full workflow, and sub-directories of playbooks
and templates.

## Install

Per-skill, machine-wide:

```bash
cp -r konmari ~/.claude/skills/
cp -r mise-en-place ~/.claude/skills/
cp -r shokunin ~/.claude/skills/
cp -r kintsugi ~/.claude/skills/
```

Or all at once:

```bash
cp -r konmari mise-en-place shokunin kintsugi ~/.claude/skills/
```

Or symlink (recommended while iterating — edits here show up
immediately in every session):

```bash
for s in konmari mise-en-place shokunin kintsugi; do
  ln -s "$(pwd)/$s" ~/.claude/skills/"$s"
done
```

## Why these shapes

Real-world methods have done the hard thinking. Marie Kondo's five
categories already solve the "what do I look at first?" problem.
French mise-en-place already encodes the dependency order of kitchen
prep. A shokunin's craft-lenses are older than software. Using them
means not re-inventing structure every time.

Each skill in this repo follows the same pattern:

- `SKILL.md` — frontmatter with trigger description; workflow;
  invariants.
- `README.md` — install + invocation.
- A sub-directory of playbooks (`categories/`, `stations/`, `lenses/`,
  `phases/`) that the skill loads one at a time to keep context lean.
- `templates/` — fixed output shapes for reports, reviews, commits.

That shape is not enforced — it's the convention these four skills
share. New skills can copy it or depart, whichever is more honest for
the method being encoded.

## How they fit together

A possible flow across a single task:

1. **mise-en-place** at session start. Catches the blocker that
   would have wasted the next hour.
2. **kintsugi** if the task is a bug. Phase 1 surfaces scope before
   any code lands.
3. **shokunin** as a self-review pass before opening the PR.
4. **konmari** periodically (quarterly? before a milestone?) to keep
   the codebase lean.

None of them depend on each other — they're siblings, not layers.

## Status

Early. Shapes will evolve as I use them on real work. PRs / issues
welcome.

## License

MIT.
