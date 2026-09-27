# asymptote — an agent skill for publication-quality figures with Asymptote

A [Claude Code](https://claude.com/claude-code) / [omp](https://github.com/can1357/oh-my-pi)-style agent skill that teaches LLMs to write **correct Asymptote** (v3.15) — the LaTeX-native vector graphics language. Covers the real language semantics (it looks like C++ but is not), the module library (`graph`, `three`/`graph3`, `contour`, `palette`, `flowchart`, `solids`, …), LaTeX embedding, and an `asy` compile gate.

Built from the official [Asymptote manual](https://asymptote.sourceforge.io) (227 pp, v3.15): signatures verbatim, every bundled example compile-tested with Asymptote 3.15 + MiKTeX + Ghostscript.

## What's inside

```
asymptote/
├── SKILL.md                   # core: mental model, C++ de-teaching traps (14), compile gate, module routing
├── references/
│   ├── language-core.md       # types, pairs (complex algebra), paths vs guides, pens, transforms, pictures
│   ├── language-prog.md       # functions, structs (reference semantics!), arrays, imports, Map/Set/Queue
│   ├── drawing.md             # draw/fill/clip/label, Bezier connectors (tension/curl/{dir}), patterns
│   ├── graph-2d.md            # axes/ticks/scales (log/broken), legends, LaTeX embedding (asymptote.sty), palettes
│   ├── graph-3d.md            # surfaces, projections, lights/materials, 3D axes, solids of revolution, PRC/WebGL
│   ├── modules.md             # contour, markers, patterns, flowchart, animations, ode, CLI flags
│   └── examples.md            # 8 compile-proven worked figures
└── evals/evals.json           # 3 eval prompts + assertions (skill-creator format)
```

## Install

```bash
git clone https://github.com/Errnolink/asymptote-skill.git
cp -r asymptote-skill ~/.claude/skills/asymptote        # Claude Code
# or: cp -r asymptote-skill ~/.agents/skills/asymptote  # omp and similar
```

Requirements: [Asymptote](https://asymptote.sourceforge.io) 3.x with a TeX toolchain (labels are typeset by LaTeX) and Ghostscript. The skill's compile gate is `asy -f pdf file.asy`.

## Highlights

- **The C++ de-teaching list** — no postfix `++`/`--` (`--` is a path join), int `/` returns real, `pair` multiplication is complex multiplication, structs are reference types, transforms apply rightmost-first.
- **Gate-learned traps** — sampler hits singularities (`sin(x)/x` needs a zero-guard); parametric `surface()` overloads live in `graph3` not bare `three`; headless 3D rasterization hangs without `settings.render=0`; flowchart block pen-arity.

## Eval results

3 prompts × with/without skill (damped oscillation plot, vector 3D surface, decision flowchart):

- with skill: **12/12 assertions** — all compile, correct module APIs, ticked axes, `render=0` vector output, flowchart module + deferred connector wrapper + labeled branches
- baseline: 10/12 — compiles, but the flowchart was hand-rolled (no module, no connector API)

## License

MIT for this skill's text. It documents Asymptote (LGPL, by Andy Hammerlindl, John Bowman, Tom Prince); signatures and names are facts from its manual, all prose and trap write-ups here are original.
