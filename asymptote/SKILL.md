---
name: asymptote
description: Draw publication-quality 2D/3D vector figures with Asymptote (v3.15), the LaTeX-native vector graphics language. Use this skill whenever the task involves asymptote, .asy files, vector figures or plots for LaTeX papers/notes, function graphs with axes, density/contour plots, 3D surfaces embedded in PDF, geometry diagrams, flowcharts or Feynman diagrams in Asymptote, animations, or fixing asy compile errors — even if the user just says "draw/plot this figure" in a LaTeX context. Contains the real language semantics (it looks like C++ but is NOT), module APIs, and a compile gate.
compatibility: asy 3.x (this machine: C:\Program Files\Asymptote\asy.exe, config.asy prewired to MiKTeX pdflatex + Ghostscript)
---

# Asymptote figure drawing

Asymptote is a coordinate-based vector graphics **programming language**: a script (`.asy`) describes figures in user coordinates; labels/equations are typeset by LaTeX; output is PDF/PS/SVG/WebGL. Version documented: 3.15.

## The mental model (get this right first)

The syntax looks like C++ **but is not C++** — most silent bugs come from C++ habits. Asymptote is mathematically oriented: a `pair` is a complex number, `*` multiplies complexly, implicit literal coefficients exist (`3x`).

Three layers to every figure:
1. **Plain drawing** (`draw`/`fill`/`clip`/`label` over paths) — no imports needed.
2. **Modules** add domains: `import graph;` (2D plots), `import three;` (3D), `import contour;`, `import flowchart;`, `import palette;`, …
3. **A picture canvas** (`currentpicture`) with `size()`/`unitsize()` controlling final scaling.

## Canonical minimal figure + compile gate

```asy
// fig.asy
size(200);
import graph;
real f(real x) {return x == 0 ? 1 : sin(x)/x;}  // guard: sampling hits x=0 → Divide by zero
draw(graph(f,-10,10),blue);
xaxis("$x$",BottomTop,LeftTicks);
yaxis("$y$",LeftRight,RightTicks);
label("$f(x)=\frac{\sin x}{x}$",(2,0.6),N);
```
```bash
asy -f pdf fig.asy   # exit 0 + fig.pdf = pass
```
On this machine `asy` is at `C:\Program Files\Asymptote\asy.exe` (MSYS: `asy.cmd` on PATH); `~/.asy/config.asy` already pins `tex="pdflatex"` + MiKTeX + Ghostscript, so plain `asy -f pdf file.asy` just works. MiKTeX prints "security risk: elevated privileges" warnings here — harmless noise. No `asy` anywhere → fall back to the Asymptote Web Application (`asy` remote API, manual ch. 14) or install before drawing.

## Core drawing loop

1. Decide the domain → read the matching reference file (table below).
2. Write the .asy top-down: `size(...)` → `import`s → functions/data → `draw`/`fill` → labels last (labels are placed by direction relative to coordinates).
3. Compile-gate; on errors read the line numbers — asy reports `file.asy: line.col: error` precisely.
4. Iterate visually only after it compiles.

## Language quick semantics (the C++ de-teaching list)

1. **No postfix `++`/`--`** — `i++` is illegal; `--` between points is a path join. Use `++i`; emulate postfix with `(++i-1)`.
2. `/` on two ints returns a **real**; integer division is `#`; `%` takes the sign of the *divisor*; `^` is power (`**` synonym).
3. `pair z=(3,4)` — `z*x` is complex multiplication (rotates+scales); conjugate via `conj(z)`; members `z.x,z.y` are read-only. `I=(0,1)`. `length(z)`/`abs(z)`.
4. `path` vs `guide`: a `guide` is an unresolved spline — build it incrementally in loops (linear cost); converting/copying `path` in a loop is quadratic. `path p=g;` resolves.
5. Transforms compose like matrix product: in `rotate(45)*xscale(2)*p` the **rightmost applies first**. Builders: `shift scale rotate reflect slant`; `inverse(T)`.
6. Pen algebra: `dashed+red+0.8` (attribute union, rightmost wins, real→linewidth in bp); `0.5*red` scales color toward black; `red+green` adds colors.
7. `size(200)` scales the whole picture into 200bp; `size(0,100)` leaves x unconstrained; `unitsize(1cm)` fixes user-unit size; add `+0.5bp` when exact-bounding (`size(100.5)`). `cm/pt/mm/inch/bp` literals exist.
8. Structs are **reference types**: `T b=a;` aliases; `alias(a,b)` tests identity. Constructors are `void operator init(...)`.
9. Path connectors: `--` straight, `..` spline, `::` = `..tension atleast 1..`, `---` = `..tension atleast infinity..`; `{dir}` fixes tangent at a node; `..controls c0 and c1..` explicit; `cycle` closes; `&` concatenates paths; `^^` groups into `path[]` (pen lifts — ring/evenodd fills).
10. Labels are **LaTeX strings**: `label("$A$",(0,0),SW)` — compass `N/S/E/W/NE/...`, `2N` scales; on-path labels use real fraction positions.
11. Functions: `real f(real x){return ...;}`; anonymous: `new real(real x){return ...;}`; default args `f(real x, pen p=currentpen)`; named calls `f(p=red)`; `var` infers locals.
12. Arrays: `int[] a={1,2,3}; a.push(4); a[1:3]` slice; `sequence(n)`, `sort(a)`, `a.cyclic=true`.

## Which reference to read next

| You are drawing / fixing | Read |
|---|---|
| Lines, shapes, fills, arrows, labels, Bezier paths, clipping, hatching basics | `references/drawing.md` |
| Types, pairs, paths/guides, pens, transforms, frames/pictures, deferred drawing | `references/language-core.md` |
| Functions, structs, arrays, iterators, imports, collections (Map/Set/Queue) | `references/language-prog.md` |
| 2D function plots, axes/ticks, scales (log/broken), legends, LaTeX embedding, palettes/density images | `references/graph-2d.md` |
| 3D: surfaces, projections, lights/materials, 3D axes, solids of revolution, tubes, PRC/WebGL | `references/graph-3d.md` |
| Contours, markers, patterns, slopefields, flowcharts, animations, misc modules, CLI flags | `references/modules.md` |
| Complete worked figures to copy patterns from | `references/examples.md` |

**API discipline**: signatures in the references are verbatim from the manual. If a call fails with "no matching function" or wrong output, re-check the signature there — argument ORDER (e.g. `xaxis(string, position, ticks, bool above)`) is where guesses break.

## Top traps (silent wrongness or confusing errors)

1. C++ habits (list above) — especially postfix `++`, int `/`, and elementwise-assuming `pair` math.
2. `..tension 2..` needs **spaces**: `..tension 2 ..` — `2..x` misparses.
3. `draw(g,Arrow)` is a draw-option, not a pen; `arrow=` named arg for full control. Arrow tips on `to[]`-style short paths don't exist here — this is not circuitikz/tikz.
4. Label placement: direction goes AFTER the position; `label("txt",(1,1),2SE)` etc. Missing direction defaults to centered (often collides).
5. `clip(pic,path)` without a picture argument clips `currentpicture` permanently — scope a `picture` for local clips (Venn idiom in examples.md).
6. Interactive `asy` (REPL) vs batch: batch mode needs explicit `size()`; without it the figure may collapse to a point / rely on unitsize.
7. 3D PDF: plain `draw(surface)` rasterizes unless `settings.render=0` (vector) or PRC enabled; `settings.prc=true` embeds interactive 3D (Adobe Reader only).
8. External TeX: label errors like "Cannot execute latex" mean the TeX toolchain isn't on asy's `texpath` (config.asy here handles it).
9. `write()` outputs to stdout only — no `print`. `//` comments; block comments `/* */`; no `#`.
10. Missing import = "no matching function" errors at draw time (e.g. `graph` for axes, `three` for any `triple`/`surface`).
11. `graph(f,a,b)` samples inside the interval — any singularity the sampler hits (`sin(x)/x` at 0) is a hard "Divide by zero" runtime error. Guard removable singularities with a ternary in the function body.
12. Parametric `surface(triple f(pair), a, b, nu, nv)` is a **graph3** overload — importing only `three` yields "no matching function". `import graph3;` covers 3D axes too.
13. Headless/batch 3D without `settings.render=0` starts software rasterization — can run minutes or hang. `render=0` gives vector output but pushes labels through the latex+dvips+gs outline pipeline (`plain_Label.asy` runtime 0x2 = a missing `dvips` config entry).
14. `rectangle("txt",pos,fillpen,drawpen)` takes TWO pens in the 2-pen overload — a third pen misbinds into a `real` and fails with "no matching function" (flowchart).

## Workflow rules

- Prefer a module over hand-rolled math (`graph` for axes/plots, `contour` for level sets, `solids` for revolutions, `flowchart` for block diagrams).
- Parameterize repeated elements with functions/loops (it's a real language — use it).
- Compile-gate every figure; check output PDF page count > 0.
- For LaTeX documents: embed with `asymptote.sty` (`\begin{asy}...\end{asy}`, run `latex → asy *.asy → latex`), or compile standalone and `\includegraphics` — see `references/graph-2d.md` (LaTeX embedding section).
