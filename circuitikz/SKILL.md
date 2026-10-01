---
name: circuitikz
description: Draw electronic circuit schematics in LaTeX with CircuiTikZ (v1.8.7). Use this skill whenever the task involves circuitikz, TikZ circuits, drawing electronic schematics (resistors, capacitors, diodes, transistors, op-amps, logic gates, muxes, chips, transformers, block diagrams, RF), LaTeX circuit figures for exams/papers, or debugging/fixing circuitikz compile errors or wrong-looking circuit output — even if the user just says "draw this circuit" inside any LaTeX document. Contains the verbatim component vocabulary, label/voltage/current semantics, anchors, and a tectonic compile gate.
compatibility: tectonic (or any LaTeX) with circuitikz >= 1.4 recommended; tikz >= 3.1.5b
---

# CircuiTikZ circuit drawing

CircuiTikZ is a TikZ extension: a circuit is a `tikzpicture` (alias: `circuitikz` environment) whose **paths carry bipoles** and whose **nodes are multi-terminal devices**. Everything below uses v1.8.7 conventions.

## The mental model (get this right first)

Two kinds of components, two placement syntaxes — choosing wrong is the #1 error source:

1. **Path-style (bipoles)** — placed *along* a wire segment:
   ```latex
   \draw (0,0) to[R=$R_1$, i=$i_1$] (2,0);
   ```
2. **Node-style (multipoles: transistors, op amps, gates, transformers, chips)** — placed *at* a point, connected later via anchors:
   ```latex
   \draw (0,0) node[npn](Q1){Q1} (Q1.B) -- ++(-1,0);
   ```

Which kind a component is: check the inventory tables in `references/components-*.md`. A path-style component can usually also be used as a node by appending `shape` to its name (`node[potentiometershape]`); the reverse is not true.

## Canonical preamble

```latex
\usepackage[siunitx, RPvoltages]{circuitikz}
```
Always load **one voltage-direction option** (`RPvoltages` = rising-potential arrows, recommended for new work; `EFvoltages` = electric-field direction). Omitting it triggers the legacy `noold` behavior with wrong battery polarity conventions. Add `american` or `european` to fix symbol conventions globally.

## Core drawing loop

1. Sketch the circuit on paper; assign coordinates to junctions. Use **relative coords** (`++(2,0)`) and named points so the circuit is movable/reusable: one absolute coordinate, everything else relative.
2. Draw each mesh as one `\draw` path: wires `--`, components `to[...]`.
3. Place multipoles with `anchor=<pin>` so the wanted pin lands on the current point: `node[op amp, noinv input up, anchor=+](OA){}`.
4. Connect with orthogonal intersections: `(FB -| OA.out)` = horizontal-through-FB × vertical-through-OA.out.
5. Decorate: labels `l=`, voltages `v=`, currents `i=`, flows `f=`; poles `o-`, `-*`.
6. **Compile-gate** (below) before delivering.

Canonical worked pattern (non-inverting amplifier, from the manual's tutorial):

```latex
\begin{circuitikz}[scale=0.8, transform shape]
\draw (0,0) node[above]{$v_i$} to[short, o-] ++(1,0)
  node[op amp, noinv input up, anchor=+](OA){\texttt{OA1}}
  (OA.-) -- ++(0,-1) coordinate(FB) to[R=$R_1$] ++(0,-2) node[ground]{}
  (FB) to[R=$R_2$, *-] (FB -| OA.out) -- (OA.out)
  to[short, *-o] ++(1,0) node[above]{$v_o$};
\end{circuitikz}
```

## Decorations quick reference

| Key | Meaning | Position specifiers |
|---|---|---|
| `l=` / `a=` | label / annotation | `l^` above, `l_` below (default: above/right) |
| `i=` | current arrow + label | `i>_`, `i^<`, … |
| `v=` | voltage (style per options) | `v^>`, `v_=`, … |
| `f=` | flow arrow | like `i=` |
| `l2^=$R$ and $5<\kilo\ohm>$` | stacked two-line label | combines with `^`/`_` |

siunitx compact form (only with the `siunitx` option): `to[R=1<\kilo\ohm>]` ≡ `l=$\SI{1}{\kilo\ohm}$`.

Full semantics (direction conventions, 4 voltage styles, generator traps, label anchors): read `references/labels-voltages.md`.

## Poles (connection points)

Attach to any bipole or wire: `o-` open circle start, `-*` filled dot end, `d-` diamond, combinable (`o-*`, `*-o`, `d-d`, …). `to[short, -*]` = wire segment that can carry a dot + labels. Poles white-out underlying wires **only within the same `\draw` path**.

## Which reference to read next

| You are drawing / fixing | Read |
|---|---|
| R, C, L, diodes, sources, batteries, instruments, fuses, connectors, grounds | `references/components-passives.md` |
| Transistors, tubes, RF, transformers, op amps, amplifiers, switches, block diagrams | `references/components-actives.md` |
| Logic gates, flip-flops, mux/demux, DIP/QFP chips, 7-segment displays | `references/components-digital.md` |
| Labels, voltages, currents, flows, poles, siunitx, FAQ traps | `references/labels-voltages.md` |
| Package options, `\ctikzset` styling, scales, colors, style files, subcircuits | `references/styling.md` |
| Complete worked circuits to copy patterns from | `references/examples.md` |

**Name discipline:** component keys are a closed vocabulary. Never write a key that is not in the inventories (or the manual). Wrong invented names are the most common silent failure (`to[sine voltage]` does not exist → it's `to[sV]`; zener is `zD`, never `DZ`; single-cell battery is `battery1`).

## Top traps (compile-breakers and silent wrongness)

1. **Name a point with `coordinate(A)`, never `node(A){}`** — nodes have size; wires get gaps. And connect to `(A.center)`/`(A.anchor)`, not bare `(A)`, when A is a node.
2. `=` or `,` inside a label/annotation must be braced: `to[R={$R_1=12V$}]`, `to[ospst={open, 3s}]`.
3. Position specifiers go **between key and `=`**: `i_>=$i_1$` — never `i_=_=` (dies with "Missing $ inserted").
4. Global negative/mixed `scale`/`xscale`/`yscale`/`rotate` are broken. Use per-component `mirror`/`invert`, or `scale + transform shape` (still limited). Fractional scale + large coordinates drifts → add `use fpu reciprocal`.
5. Don't put arrow tips or `shorten <` on paths with `to[]` components.
6. Color: use explicit `color=red` (not bare `red` style); avoid changing color mid-path — split into separate `\draw` commands. Fillable components inherit path `fill=`; full variants (`D*`) fill with the draw color.
7. Dashed/colored wire segments: separate `\draw`, or `edge[dashed, red]` (remember: edges are painted *after* nodes and don't advance the path position — restate `++(1,0)` after).
8. `to[open, v=$v$]` is the correct way to show a voltage between two existing terminals, but it draws a plain wire — never use it for dimension-style port annotations on a schematic; draw those by hand (`\draw[{Stealth}-{Stealth}] … node{$V_x$};`).
9. Closing a path around components: `--cycle` doesn't work with `to[]`; joints can leave gaps with thick lines — add `-.` pole or close on a `coordinate`.
10. babel languages with active chars: `\usetikzlibrary{babel}` after loading.
11. Externalization: use `tikzpicture`, not the `circuitikz` environment name.
12. Voltage/current direction on sources interacts with the direction option: with `RPvoltages`, `v^=`/`v_=` keep consistent passive/active conventions; mixing styles mid-document has ordering effects (§5.5.7 of the manual).
13. Anchors are only meaningful on **named** components: `to[R, n=R1]` or `to[R=R1]` (label shortcut also names it); then `(R1.Vfrom)`, `(R1.label)` etc. exist.
14. An undefined style containing a hyphen (e.g. the tutorial's `sr-ff` used without its `\tikzset{sr-ff/.style={flipflop, flipflop def={...}}}` definition) misreports as `Unknown arrow tip kind 'sr'` — it's a missing style definition, not an arrow problem.
15. Transistor/op-amp text labels sit at the component center by default (`centertransistorstext`); desenders misalign baselines — wrap with `\killdepth{}` (def once: `\def\killdepth#1{\raisebox{0pt}[\height][0pt]{#1}}`).

## Compile gate (always run before delivering)

```latex
% probe.tex
\documentclass{article}
\usepackage[siunitx, RPvoltages]{circuitikz}
\begin{document}
\begin{circuitikz}
\draw (0,0) to[R=$R_1$] (2,0);
\end{circuitikz}
\end{document}
```
```bash
tectonic probe.tex   # exit 0 + probe.pdf produced = pass
```
Put the real figure in the same harness (article + circuitikz + `\begin{circuitikz}…`). Tectonic's first run downloads a bundle (~5 min); afterwards it is warm. Any error named `Undefined shape`/`Unknown key` = invented component name or typo — check the inventory, not your intuition.

## Version awareness

This skill documents **v1.8.7** conventions; LaTeX distributions lag. Tectonic 0.17's bundle ships **1.4.6** (verify anywhere with `\pgfcircversion{}`). Consequences:

- Core vocabulary (passives, sources, transistors, op amps, gates, labels/poles/voltages) is stable across 1.4→1.8 — safe everywhere.
- Post-1.4.6 additions will fail the compile gate with `Undefined shape`/`Unknown key`: among others `xdelta` (1.8.7), jfet gate height + `component text=border` (1.8.6), auto-vif `vif queue add` + ideal filters (1.8.5), filter blocks + block inner drawings (1.8.4), oo-source anchors/windings + IGCT (1.8.3), pics support (1.8.0).
- Needing those: download `circuitikzgit.sty` from https://circuitikz.github.io/ into the document directory and `\usepackage[...]{circuitikzgit}` — that pins the current version regardless of the distribution.
- Old documents: the kernel rollback works — `\usepackage[]{circuitikz}[=v1.4.6]`.

## Workflow rules

- Reuse over redraw: wrap repeated sub-circuits in a `\newcommand` macro parameterized by name + start coordinate (see `references/examples.md`), or use subcircuits/pics for anchor-addressable blocks.
- Set document-wide styling once in the preamble via `\ctikzset` (fills, scales, thickness), not per-figure.
- When a figure looks wrong but compiles, check the trap list first — 90% of visual bugs are traps 1, 4, 6, 7, or 12 above.
