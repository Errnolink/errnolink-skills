# errnolink-skills

Unified home for agent skills I built for my own use cases. Each directory is a standalone skill — copy it into your agent's skill root (`~/.agents/skills/` on this machine).

| Skill | What it does | Source manual |
|---|---|---|
| [`asymptote/`](asymptote/) | Asymptote `.asy` figures, LaTeX plots/diagrams, compile-error debugging | Asymptote v3.15 manual, 227pp |
| [`circuitikz/`](circuitikz/) | CircuiTikZ circuit schematics in LaTeX (draw + debug) | CircuiTikZ v1.8.7 manual, 315pp |

## Install

```
cp -r <skill> ~/.agents/skills/<skill>
```

## Layout

Each skill ships:

- `SKILL.md` — core model, canonical preamble, traps, compile gate
- `references/` — topic digests distilled from the full manual
- `evals/` — with/without-skill benchmark prompts and grading

Commit history of the original standalone repos (`asymptote-skill`, `circuitikz-skill`) is preserved here via subtree merges — `git log --follow <skill>/...` reaches the original commits.
