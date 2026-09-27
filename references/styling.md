# Styling, options and configuration (§1.12, §3.3, §3.4, ch. 7)

> Read this when: choosing package options, changing component sizes/colors/thickness globally, defining style files, reusing subcircuits, or fixing color/scale weirdness.

## Package options (load-time, `\usepackage[...]{circuitikz}`)

Cultural sets (pick one of each pair):
- `american` / `european` — umbrella: currents, voltages, resistors, inductors, ports, surge arresters
- `americanvoltages` / `europeanvoltages` — +/− signs vs. arrows
- `americancurrents` / `europeancurrents`, `americanresistors` / `europeanresistors` (zig-zag vs rectangle), `americaninductors` / `europeaninductors` / `cuteinductors` (pig-tail), `americanports` / `europeanports`, `americangfsurgearrester` / `europeangfsurgearrester`
- `straightvoltages` — straight european voltage arrows

Voltage directions (**always choose one**): `RPvoltages` (rising potential, recommended) · `EFvoltages` (electric-field direction) · `oldvoltagedirection` / `nooldvoltagedirection` (legacy).

Diodes: `fulldiode` / `strokediode` / `emptydiode` — default appearance of bare `D`, `sD`, `zD`, … (explicit suffixes `*`/`-`/`o` always override).

Transistors: `arrowmos`/`noarrowmos` · `fetbodydiode`/`nofetbodydiode` · `fetsolderdot`/`nofetsolderdot` · `emptypmoscircle` · `lazymos` (simplified symbols for big circuits) · `legacytransistorstext` / `centertransistorstext` (label position).

Labels: `straightlabels` / `rotatelabels` / `smartlabels` (rotate unless near 90° multiples).

Other: `siunitx` / `nosiunitx` · `compatibility` (coexist with TikZ's own `circuits` library; prefixes keys with `*`: `to[*R]`) · `betterproportions` (legacy).

Defaults when loading with no options: `nofetsolderdot, europeancurrents, europeanvoltages, americanports, americanresistors, cuteinductors, europeangfsurgearrester, nosiunitx, noarrowmos, smartlabels, nocompatibility, centertransistorstext`.

## Relative component sizing (§3.3.1)

Everything scales off one master length; classes scale individually:

```latex
\ctikzset{bipoles/length=1.4cm}      % master bipole length
\ctikzset{resistors/scale=1.2}       % per-class scale
\ctikzset{transistors/scale=0.8}
```
Classes: `resistors, capacitors, inductors, diodes, sources, csources, instruments, mechanicals, misc, connectors, grounds, power supplies, blocks, transistors, tubes, RF, amplifiers, switches, logic ports, analog` (some families also accept `/width`, `/thickness`).

Per-element override inline: `to[R, resistors/scale=1.5]` or `node[npn, circuitikz/transistors/scale=1.2]`.

## Fill and thickness

```latex
\ctikzset{amplifiers/fill=cyan!20}   % class fill
\ctikzset{logic ports/fill=none}
\ctikzset{resistors/thickness=1.2}   % relative thickness
\ctikzset{component text=left}       % label side: left/right/up/down; border for twoportsplit
```
Direct per-component: `node[and port, fill=yellow]`, `to[generic, fill=white]`. A style fill is overridden by an explicit `fill=`; to force no-fill over a style, use `circuitikz/logic ports/fill=none` inline.

## Colors (ch. 7) — the rules that avoid glitches

- Use explicit `color=red`; the shorthand `red` fails to propagate into components on some engines.
- Don't change color mid-path (TikZ applies the *last* effective color to the whole path). Split paths instead.
- Fillable components inherit path `fill=`; "full" variants (`D*`, `tfullgeneric`) fill with the *draw* color.
- Colored background: open poles still fill white → set `\ctikzset{open poles fill=<bg>}` (or `open poles opacity`).
- Transparency: don't (poles' opacity is managed by `poles/open fill opacity` / style `open poles opacity`).

## Style files (§3.3.4–3.3.5)

Named bundles live in `circuitikz.styles` files loadable via `\ctikzloadstyle{<name>}` (e.g. the manual ships `romano`). A style file is a set of `\ctikzset`/`\tikzset` definitions; enable with `\begin{circuitikz}[<name> circuit style]`. Write your own by putting `\ctikzset{...}` keys into `<name>.sty`-style cfg and loading it — keep document styling there rather than repeating per figure.

## Reuse: macros, subcircuits, pics (§3.4)

- **Macro** (simplest): `\newcommand\myNIA[4]{...}` taking name, start coordinate, parameters; position by first coordinate only. Make internal node/coordinate names unique by prepending `#1`.
- **Subcircuit** (anchor-addressable, parameterless): `\ctikzsubcircuitdef{sub:name}{#1}{...}` + `\ctikzsubcircuitactivate{name}` then `node[sub@name]` / `(name.anchor)`; supports scale/flip/rotate.
- **pic** (parameters + transforms, v1.8.0+): `\tikzset{mypic/.pic={\draw (0,0) to[R=#1] ++(2,1) ...}}` then `pic(P){mypic=PR}`; prefixed anchors `(P-led)`. Guard against name collisions with `name prefix=`.

## Known scaling limits (§1.8–1.9)

- No global negative/unequal `xscale`/`yscale`, no global `rotate` (needs a path-routine rewrite; upstream acknowledges).
- `scale + transform shape` works but text also scales; fractional scale + big coordinates can drift → `\begin{circuitikz}[..., use fpu reciprocal]`.

Cross-checked against manual index: 40+ keys/options verified.
