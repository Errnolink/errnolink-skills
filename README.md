# circuitikz — an agent skill for drawing electronic circuits in LaTeX

A [Claude Code](https://claude.com/claude-code) / [omp](https://github.com/can1357/oh-my-pi)-style agent skill that teaches LLMs to emit **correct CircuiTikZ** (v1.8.7 conventions) — the verbatim component vocabulary, label/voltage/current semantics, anchors, traps, and a compile gate.

Built from the official [CircuiTikZ manual](https://ctan.org/pkg/circuitikz) (315 pp, v1.8.7, 2026-09-12): every component name cross-checked against the manual's index (~1262 entries), every code pattern compile-tested with [tectonic](https://tectonic-typesetting.github.io/).

## What's inside

```
circuitikz/
├── SKILL.md                        # core: path-vs-node model, drawing loop, 15 traps, compile gate
├── references/
│   ├── components-passives.md      # grounds, R/C/L, diodes, sources, instruments, connectors (361 names)
│   ├── components-actives.md       # transistors, tubes, RF, transformers, op amps, switches, blocks (197 names)
│   ├── components-digital.md       # logic gates (US/IEEE/EU), flip-flops, mux/demux, chips, 7-seg (56 names)
│   ├── labels-voltages.md          # l/a/i/v/f decorations, voltage styles, RP/EF directions, poles, FAQ
│   ├── styling.md                  # package options, ctikzset classes, colors, subcircuits/pics
│   └── examples.md                 # 8 compile-proven worked circuits
└── evals/evals.json                # 3 eval prompts + assertions (skill-creator format)
```

## Install

Drop the folder into your agent's skills directory:

```bash
git clone https://github.com/Errnolink/circuitikz-skill.git
cp -r circuitikz-skill ~/.claude/skills/circuitikz        # Claude Code
# or: cp -r circuitikz-skill ~/.agents/skills/circuitikz  # omp and similar
```

Requirements: a LaTeX toolchain with `circuitikz` (tectonic works). The skill's compile gate uses `tectonic <file>.tex`; any LaTeX engine substitutes.

## Version note

The skill documents v1.8.7, but distributions lag (tectonic 0.17's bundle ships 1.4.6). Core vocabulary is stable across 1.4→1.8; the few post-1.4.6 additions are listed in SKILL.md's *Version awareness* section together with the `circuitikzgit.sty` escape hatch.

## Eval results

3 prompts × with/without skill (bridge rectifier, common-emitter amplifier, mux→D flip-flop):

- with skill: **12/12 assertions** — tectonic-compiles, zero invented component names, correct component counts and pin labels, `v=`/`i=` decorations in place
- baselines compile but skip the voltage-direction option, skip siunitx, and hand-roll crossings

## License

MIT for this skill's text. It documents the CircuiTikZ package (LPPL 1.3.1 / GPL, by Massimo Redaelli, Stefan Lindner, Stefan Erhardt, Romano Giannetti) — component names and semantics are facts from its manual; all prose, tables, and trap write-ups here are original.
