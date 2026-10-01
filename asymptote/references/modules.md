# User & Specialty Modules, Flowcharts & CLI (§8.4–8.21, §9, §11, pp. 164–199)
> Read this when drawing: contour plots, flowchart diagrams, direction fields, markers, hatching patterns, smooth 3D implicit surfaces, math/stats utilities, animations, or invoking the Asymptote CLI compiler.

## Syntax recap

### Flowchart with Deferred Connectors (Canonical Idiom)
```asy
import flowchart;
size(0, 300);

// 1. Declare blocks: rectangle with header, diamond decision, rounded process, circle end
block bStart = rectangle(Label("Start", white), pack(Label("$x := 0$"), "$y := 1$"), (0, 3), darkblue, paleblue, red);
block bCheck = diamond(Label("$x < 10$?", blue), (0, 2), palegreen, red);
block bStep  = roundrectangle("Increment $x$", (-1.5, 1));
block bDone  = circle("Done", (0, 0));

// 2. Draw block frames
draw(bStart); draw(bCheck); draw(bStep); draw(bDone);

// 3. Connectors MUST be declared inside deferred-draw closure to resolve transformed anchors
add(new void(picture pic, transform t) {
  blockconnector operator -- = blockconnector(pic, t); // Shadows path -- with block connector
  bStart -- Down -- Arrow -- bCheck;
  bCheck -- Label("Yes", 0.5, NW) -- Left -- Down -- Arrow -- bStep;
  bStep  -- Down -- Right -- Arrow -- bDone;
  bCheck -- Label("No", 0.5, NE)  -- Right -- (1.5, 2) -- Down -- (1.5, 0) -- Arrow -- bDone;
});
```

### Contour Plot with Dynamic Pens & Labels
```asy
import contour;
size(200);
real f(real x, real y) { return x^2 - y^2; }
real[] c = uniform(-1, 1, 10); // 10 evenly spaced levels

pen[] pens = sequence(new pen(int i) {
  return (c[i] >= 0 ? solid : dashed) + (c[i] == 0 ? 1.5bp : 0.5bp);
}, c.length);

Label[] labels = sequence(new Label(int i) {
  return Label(c[i] != 0 ? (string) c[i] : "", Relative(unitrand()), (0, 0), UnFill(1bp));
}, c.length);

draw(labels, contour(f, (-2, -2), (2, 2), c), pens);
```

---

## Vocabulary

### Contour Lines (§8.9)
- `guide[][] contour(real f(real, real), pair a, pair b, real[] c, int nx=ngraph, int ny=nx, interpolate join=operator --, int subsample=1);` — Contour curves of function `f` over bounding box `(a,b)` at elevation levels `c`.
- `guide[][] contour(real[][] f, pair a, pair b, real[] c, interpolate join=operator --, int subsample=1);` — Contours from uniform 2D grid matrix `f`.
- `guide[][] contour(pair[][] z, real[][] f, real[] c, interpolate join=operator --, int subsample=1);` — Contours from non-overlapping regular mesh `z` with values `f`.
- `guide[][] contour(pair[] z, real[] f, real[] c, interpolate join=operator --);` — Contours from scattered points `z` and values `f` (Delaunay triangulation backend).
- `void draw(picture pic=currentpicture, Label[] L=new Label[], guide[][] g, pen p=currentpen);` — Draw contour curves with uniform pen.
- `void draw(picture pic=currentpicture, Label[] L=new Label[], guide[][] g, pen[] p);` — Draw contour curves with per-level pen array `p`.
- `int[][] triangulate(pair[] z);` — Delaunay triangulation of scattered 2D points; returns vertex-index triples.

### Markers & Angle Marking (§8.13, §8.1)
- `markroutine markinterval(int n=1, frame f, bool rotated=false);` — Places `n` copies of frame `f` centered within uniformly spaced arclength intervals (rotated by tangent if true).
- `frame stickframe(int n=1, real size=0, pair space=0, real angle=0, pair offset=0, pen p=currentpen);` — Tick mark frame (hash marks on segments).
- `frame circlebarframe(int n=1, real barsize=0, real radius=0, real angle=0, pair offset=0, pen p=currentpen, filltype filltype=NoFill, bool above=false);` — Circular frame with crossbar.
- `frame crossframe(int n=3, real size=0, pair space=0, real angle=0, pair offset=0, pen p=currentpen);` — Symmetric cross marker frame.
- `frame tildeframe(int n=1, real size=0, pair space=0, real angle=0, pair offset=0, pen p=currentpen);` — Tilde marker frame.
- `marker StickIntervalMarker, CrossIntervalMarker, CircleBarIntervalMarker, TildeIntervalMarker;` — Predefined interval markers built from above frames.
- `void markangle(picture pic=currentpicture, Label L="", int n=1, real radius=0, real space=0, pair A, pair O, pair B, arrowbar arrow=None, pen p=currentpen, margin margin=NoMargin, marker marker=nomarker);` — Marks angle $\angle AOB$ with vertex at $O$ (sign-aware orientation).
- `marker marker(path g, markroutine markroutine=marknodes, pen p=currentpen, filltype filltype=NoFill, bool above=true);` — Marker constructed from path `g`.
- `marker marker(frame f, markroutine markroutine=marknodes, bool above=true);` — Marker constructed from arbitrary frame `f`.
- `markroutine marknodes;` — Mark routine placing markers at every Bézier node of a path.
- `markroutine markuniform(bool centered=false, int n, bool rotated=false);` — Places marks at `n` uniformly spaced arclength points.
- `markroutine markuniform(pair z(real t), real a, real b, int n);` — Places marks at parametrically sampled points $z(t)$.
- `path polygon(int n);` / `path cross(int n, bool round=true, real r=0);` — Regular polygon and star-cross marker path generators.
- `marker[] Mark, MarkFill;` — Predefined standard marker arrays (circles, squares, triangles, crosses).
- `marker markeronpath [unverified];` / `marker markcurve(interpolate join=Spline|Straight) [unverified];` — *Note: In standard Asymptote, `Straight` (`operator --`) and `Spline` (`operator ..`) are interpolation joins for `graph()`, not marker constructors.*

### Patterns & PostScript Hatching (§8.15, §6.3)
- `picture hatch(real H=5mm, pair dir=NE, pen p=currentpen);` — Generates linear hatching pattern with spacing `H` along direction `dir`.
- `picture crosshatch(real H=5mm, pen p=currentpen);` — Generates orthogonal crosshatching pattern with spacing `H`.
- `picture tile(real Hx=5mm, real Hy=0, pen p=currentpen, filltype filltype=NoFill);` — Generates repetitive rectangular tile pattern.
- `picture checker(real Hx=5mm, real Hy=0, pen p=currentpen);` — Generates checkerboard pattern.
- `picture brick(real Hx=5mm, real Hy=0, pen p=currentpen);` — Generates staggered brickwork pattern.
- `void add(string name, picture pic, pair lb=0, pair rt=0);` — Registers pattern `pic` into global dictionary `currentpatterns` under `name`.
- `pen pattern(string name);` — Returns a pen that fills using the registered pattern `name`.
- *Note on `hatch("hspacing,hslope")` [unverified]:* Standard Asymptote does not take comma-delimited strings; pass real spacing and pair direction directly: `hatch(3mm, NW)`.

### Slope Fields & ODE Curves (§8.17)
- `picture slopefield(real f(real, real), pair a, pair b, int nx=nmesh, int ny=nx, real tickfactor=0.5, pen p=currentpen, arrowbar arrow=None);` — Direction field picture for $\frac{dy}{dx} = f(x,y)$ over box `[a,b]`.
- `picture slopefield(real f(real), pair a, pair b, int nx=nmesh, int ny=nx, real tickfactor=0.5, pen p=currentpen, arrowbar arrow=None);` — Direction field picture for autonomous $\frac{dy}{dx} = f(x)$.
- `path curve(pair c, real f(real, real), pair a, pair b);` — Integral curve passing through initial value $c=(x_0,y_0)$ across bounds `[a,b]`.
- `path curve(pair c, real f(real), pair a, pair b);` — Integral curve for autonomous differential equation passing through $c$.

### Smooth 3D Implicit Surfaces (§8.18)
- `surface implicitsurface(real f(triple)=null, real ff(real,real,real)=null, triple a, triple b, int n=nmesh, bool keyword overlapedges=false, int keyword nx=n, int keyword ny=n, int keyword nz=n, int keyword maxdepth=8, bool usetriangles=true);` — Generates smooth surface solving $f(x,y,z)=0$ within bounding box `(a,b)`.

### Interpolation & Rounded Paths (§8.12, §8.16)
- `import interpolate;` — Provides Lagrange, Hermite, and cubic spline interpolation backend algorithms for data approximation (see `interpolate1.asy`).
- `import roundedpath;` — Replaces sharp path vertices with circular arcs; `path roundedpath(path g, real r);` [unverified].

### Math & Path Queries Quick Reference (§8.14)
- `void drawline(picture pic=currentpicture, pair P, pair Q, pen p=currentpen);` — Draws infinite line through points $P$ and $Q$ clipped to visible canvas without expanding bounds.
- `real intersect(triple P, triple Q, triple n, triple Z);` — Intersection time $t$ of line extension $P + t(Q-P)$ with plane through $Z$ with normal $n$.
- `triple intersectionpoint(triple n0, triple P0, triple n1, triple P1);` — Arbitrary point on intersection line of two planes; returns `(infinity,infinity,infinity)` if parallel.
- `pair[] quarticroots(real a, real b, real c, real d, real e);` — All 4 complex roots of $ax^4+bx^3+cx^2+dx+e=0$.
- `real time(path g, real x, int n=0, real fuzz=-1);` — Time parameter of $n$-th intersection of path $g$ with vertical line at $X = x$.
- `real time(path g, explicit pair z, int n=0, real fuzz=-1);` — Time parameter of $n$-th intersection of path $g$ with horizontal line $Y = z.y$.
- `real value(path g, real x, int n=0, real fuzz=-1);` — $y$-coordinate of $n$-th intersection of path $g$ with vertical line at $x$.
- `real value(path g, explicit pair z, int n=0, real fuzz=-1);` — $x$-coordinate of $n$-th intersection of path $g$ with horizontal line at $y=z.y$.
- `real slope(path g, real x, int n=0, real fuzz=-1);` — Tangent slope $\frac{dy}{dx}$ of path $g$ at $n$-th intersection with vertical line $x$.
- `real slope(path g, explicit pair z, int n=0, real fuzz=-1);` — Tangent slope $\frac{dy}{dx}$ of path $g$ at $n$-th intersection with horizontal line $y=z.y$.
- `int[][] segment(bool[] b);` — Index intervals `[start, end]` of consecutive `true` sequences in boolean array `b`.
- `real[] partialsum(real[] a);` / `real[] partialsum(real[] a, real[] dx);` — Running prefix sums (or $dx$-weighted Riemann sums).
- `bool increasing(real[] a, bool strict=false);` — Tests if array is weakly or strictly monotonically increasing.
- `int unique(real[] a, real x);` — Inserts $x$ into sorted array $a$ maintaining order; returns index of $x$.
- `bool lexorder(pair a, pair b);` / `bool lexorder(triple a, triple b);` — Strict lexicographic ordering predicate ($x$ then $y$ then $z$).

### Statistics & Least Squares Quick Reference (§8.20, §8.1)
- `real Gaussrand();` — Gaussian (normal) pseudo-random number generator $\mathcal{N}(0,1)$.
- `real Gaussian(real x);` — Standard normal probability density function $\frac{1}{\sqrt{2\pi}}e^{-x^2/2}$.
- `int bins(real[] a);` — Optimal histogram bin count using Shimazaki–Shinomoto optimization algorithm.
- `void histogram(picture pic=currentpicture, real[] a, real min, real max, int N, bool normalize=true, real low=0, pen fillpen=currentpen, pen drawpen=nullpen, bool bars=true);` — Generates frequency histogram for dataset `a`.
- `linefit leastsquares(real[] x, real[] y);` — Linear least-squares fit; returns struct `linefit` with fields `.m` (slope), `.b` (intercept), `.dm` (slope error).

### Geometry Module (§8.10 — POINTER ONLY)
- `import geometry;` — Contributed by Philippe Ivaldi. **POINTER ONLY**: External documentation archived at `piprime.fr` (links in manual). Provides dynamic Euclidean geometry constructions, `triangle` structure, ruler-and-compass operations, and perpendicular angle symbols. *Do not guess signatures without external docs.*

### Flowcharts (§9.13)
- `block rectangle(object header, object body, pair center=(0,0), pen headerpen=mediumgray, pen bodypen=invisible, pen drawpen=currentpen, real dx=3, real minheaderwidth=minblockwidth, real minheaderheight=minblockwidth, real minbodywidth=minblockheight, real minbodyheight=minblockheight);` — Block with distinct header and body compartments.
- `block rectangle(object body, pair center=(0,0), pen fillpen=invisible, pen drawpen=currentpen, real dx=3, real minwidth=minblockwidth, real minheight=minblockheight);` — Standard rectangular process block.
- `block parallelogram(object body, pair center=(0,0), pen fillpen=invisible, pen drawpen=currentpen, real dx=3, real slope=2, real minwidth=minblockwidth, real minheight=minblockheight);` — Input/Output parallelogram block.
- `block diamond(object body, pair center=(0,0), pen fillpen=invisible, pen drawpen=currentpen, real ds=5, real dw=1, real height=20, real minwidth=minblockwidth, real minheight=minblockheight);` — Decision diamond block.
- `block circle(object body, pair center=(0,0), pen fillpen=invisible, pen drawpen=currentpen, real dr=3, real mindiameter=mincirclediameter);` — Circular terminator/connector block.
- `block roundrectangle(object body, pair center=(0,0), pen fillpen=invisible, pen drawpen=currentpen, real ds=5, real dw=0, real minwidth=minblockwidth, real minheight=minblockheight);` — Rounded corner rectangular block.
- `block bevel(object body, pair center=(0,0), pen fillpen=invisible, pen drawpen=currentpen, real dh=5, real dw=5, real minwidth=minblockwidth, real minheight=minblockheight);` — Chamfered edge rectangular block.
- `pair block.top(transform t=identity());` / `block.bottom(...)` / `block.left(...)` / `block.right(...)` — Compass anchor points on block boundary.
- `pair block.topleft(transform t=identity());` / `block.topright(...)` / `block.bottomleft(...)` / `block.bottomright(...)` — Corner anchor points on block boundary.
- `pair block.position(real x, transform t=identity());` — Boundary coordinate at relative perimeter fraction $x \in [0, 1]$.
- `pair block.center;` — Center of block in user coordinates.
- `pair block.size;` — Size of block bounding box in PostScript coordinates.
- `frame block.draw(pen p=currentpen);` — Renders block to frame.
- `path path(pair point[], ... flowdir dir[]);` — Generates right-angled path through points along specified directions (`Horizontal` or `Vertical`).
- `blockconnector operator -- = blockconnector(picture pic, transform t);` — Operator constructor inside deferred closure connecting flowchart blocks with orthogonal routing.
- Flow directions: `Right`, `Left`, `Up`, `Down`, `Horizontal`, `Vertical`, `Arrow` (terminating arrowhead).

### Animation & Animate (§9.1, §9.2)
- `import animation;` — Generates multi-frame animations merged via ImageMagick `magick` into GIF or MPEG (`animation.movie()` [unverified]).
- `import animate;` — Extends `animation` using LaTeX `animate.sty` to generate interactive PDF animations with embedded playback controls.
- `string pdf(fit fit=NoBox, real delay=animationdelay, string options="", bool keep=settings.keep, bool multipage=true);` — Generates PDF movie from accumulated frames; accepts any `animate.sty` configuration key in `options`.

### Specialty Modules One-Liners (§9.12, §9.18, §9.19, §9.29)
- `import feynman;` — Feynman diagram primitives (lines, loops, propagators) by Martin Wiebusch (examples: `eetomumu.asy`, `fermi.asy`; no API in manual).
- `import ode;` — Explicit numerical integration schemes for systems of ordinary differential equations (example: `odetest.asy`; no API in manual).
- `import obj;` — Imports 3D Wavefront `.obj` mesh files and converts them to Asymptote `surface` (examples: `galleon.asy`, `triceratops.asy`; no API in manual).
- `import trembling;` — Hand-drawn wavy line filter by Philippe Ivaldi (example: `floatingdisk.asy`; no API in manual).

### Path-Aligned Labels: Labelpath (§9.14, §9.15)
- `void labelpath(picture pic=currentpicture, Label L, path g, string justify=Centered, pen p=currentpen);` — Typesets label along curved path `g` using PSTricks `\pstextpath` (requires `-tex latex`).
- `justify` values: `LeftJustified`, `Centered`, `RightJustified`.
- `import labelpath3;` — 3D curved labelpath implementation by Jens Schwaiger (does not require PSTricks).

### Command-Line Interface (CLI) Essentials (§11)
- `-f, -outformat format` — Output format: `eps` (default for latex/tex), `pdf` (default for pdflatex/xelatex/lualatex), `svg`, `png`, `html` (WebGL 3D), `prc`, `v3d`.
- `-o, -outname name` — Output directory or filename **prefix** (not strict filename). `-o -` streams raw file to standard output.
- `-tex engine` — TeX typesetting engine: `latex|pdflatex|xelatex|lualatex|tex|pdftex|luatex|context|none`.
- `-V, -View` — Open rendered output in default system viewer. Negate with `-noV` for non-interactive compile gates.
- `-safe` / `-nosafe` — Sandbox safety: `-safe` (default true) disables `system(string)` calls; `-nosafe` enables arbitrary shell calls.
- `-c, -command str` — Executes Asymptote statements directly from CLI before parsing input files; repeatable.
- `-q, -quiet` — Suppresses diagnostic banners and non-interactive output.
- `-k, -keep` / `-keepaux` — Retains intermediate `.tex`, `.eps`, and `.aux` files during processing.
- `-render n` — Render 3D graphics or raster outputs at $n$ pixels per bp (default 2).
- Boolean flag rule: Negate any boolean flag by prepending `no` (e.g., `-noV`, `-nobatchView`, `-nosafe`).

### Configuration File (`config.asy`) (§11)
Located at `%USERPROFILE%\.asy\config.asy` (Windows) or `~/.asy/config.asy` (Unix). Configures persistent settings using long option names:
```asy
import settings;
outformat = "pdf";
batchView = false;       // Headless compilation
interactiveView = false;
interactiveMask = true;  // Mask floating point exceptions in REPL
safe = true;
```

---

## Options/parameters

| Context / Function | Parameter | Type | Default | Description |
|---|---|---|---|---|
| `contour(f, a, b, c, ...)` | `nx`, `ny` | `int` | `ngraph` (100) | Grid sampling resolution in $x$ and $y$. |
| | `join` | `interpolate` | `operator --` | Curve connector: `operator --` (linear) or `operator ..` (spline). |
| | `subsample` | `int` | 1 | Number of internal points per unit cell. |
| `slopefield(...)` | `tickfactor` | `real` | 0.5 | Fraction of cell dimension used for tick vector length. |
| | `arrow` | `arrowbar` | `None` | Arrowhead decoration on slope vector endpoints. |
| `implicitsurface(...)` | `overlapedges` | `bool` | `false` | Set `true` to eliminate renderer patch-boundary see-through seams. |
| | `nx`, `ny`, `nz` | `int` | `nmesh` | Axis-specific subdivision mesh bounds (keyword arguments). |
| | `maxdepth` | `int` | 8 | Octree subdivision refinement limit. |
| `hatch(...)` | `H` | `real` | `5mm` | Spacing between adjacent hatch lines. |
| | `dir` | `pair` | `NE` | Orientation vector for hatch lines. |
| `markinterval(...)` | `n` | `int` | 1 | Count of frames centered per arclength interval. |
| | `rotated` | `bool` | `false` | Rotate frame to align with curve tangent. |
| `markangle(...)` | `radius` | `real` | 0 | Arc radius; 0 selects default proportional radius. |
| | `space` | `real` | 0 | Spacing between multiple concentric arc rings (`n > 1`). |
| `rectangle(...)` | `dx` | `real` | 3 | Inner padding in PostScript bp around text body/header. |
| | `minwidth`, `minheight` | `real` | `minblockwidth` | Minimum bounding dimension constraints. |
| `diamond(...)` | `ds`, `dw` | `real` | 5, 1 | Vertex spacing and corner width adjustments. |
| `animate.pdf(...)` | `fit` | `fit` | `NoBox` | PDF bounding box fitting mode. |
| | `delay` | `real` | `animationdelay` | Frame delay in milliseconds. |
| | `options` | `string` | `""` | Comma-delimited LaTeX `animate.sty` package keys. |
| `labelpath(...)` | `justify` | `string` | `Centered` | Alignment mode: `LeftJustified`, `Centered`, `RightJustified`. |

---

## Traps

1. **Flowchart connectors require the deferred closure**: Writing `block1 -- block2` directly in global scope calls path connectors, producing broken or misplaced straight lines. Connectors MUST be inside `add(new void(picture pic, transform t) { blockconnector operator -- = blockconnector(pic, t); ... });` so anchors evaluate under final scaling `t`.
2. **Block anchors require transform `t`**: Calling `b.right()` without passing `t` evaluates the anchor in unscaled user coordinates, detaching connectors when picture dimensions scale. Always call `b.right(t)`, `b.top(t)`, etc.
3. **CLI default output is EPS, not PDF**: With standard `-tex latex`, running `asy file` creates `file.eps`. A build pipeline expecting PDF must pass `-f pdf` or set `outformat = "pdf";` in `config.asy`.
4. **CLI `-o` is a path prefix, not a target filename**: `asy -f pdf -o out dir/fig.asy` writes `out.pdf` (or `out_0.pdf`), not `out`. If `-o mydir/` is used, ensure directory exists or output prefixes will collide.
5. **`-V` launches interactive viewer by default**: On Windows, single-file compilation defaults `-V` to open Acrobat/GSview, blocking headless scripts. Headless CI builds must pass `-noV -q`.
6. **`slopefield()` returns a `picture`, not a `path`**: Writing `draw(slopefield(...))` throws a compile error. You must add the picture: `add(slopefield(...));`.
7. **Patterns require two-step registration**: `fill(g, pattern("hatch"))` crashes unless `add("hatch", hatch());` was previously invoked to populate `currentpatterns`. Pattern names are global strings.
8. **Hatching takes parameters, not strings**: The prompt idiom `hatch("hspacing,hslope")` is invalid Asymptote [unverified]. The actual signature is `picture hatch(real H=5mm, pair dir=NE, pen p=currentpen)`.
9. **`Straight` and `Spline` are graph join types, not markers**: `Straight` (`operator --`) and `Spline` (`operator ..`) are interpolation arguments for `graph()`. Passing them to `marker()` fails; markers use `marknodes` or `markuniform`.
10. **`smoothcontour3` seams (`overlapedges`)**: The default `overlapedges = false` leaves see-through wireframe gaps between patches in 3D viewers. Always supply `overlapedges = true`.
11. **`smoothcontour3` keyword syntax**: Parameters `nx`, `ny`, `nz`, `maxdepth` are declared with the Asymptote `keyword` qualifier. They must be invoked as named arguments (`maxdepth = 10`), not positional.
12. **Contour spline overshooting**: `contour(..., join = operator ..)` causes cubic spline overshoot loops near steep saddle points. Use default `operator --` for robust scientific isolines.
13. **`math` path query `explicit pair z` trap**: `time(g, (0, 2))` does NOT query the intersection with point `(0,2)`. It queries intersection with the horizontal line $y = 2.0$ passing through `(0, z.y)`. To query at vertical line $x = 2$, use `time(g, 2.0)`.
14. **Element-wise array filtering via `null`**: In Asymptote, conditional filtering of data arrays uses `a = (cond ? a : null);`. Passing `null` drops mismatched elements; it is not a C++ null pointer exception.
15. **Geometry module has zero in-manual API**: §8.10 is purely an external hyperlink pointer. Never generate synthetic geometry functions (e.g., `bisector`, `circle_tangent`); only `triangle` and perpendicular marks are noted in the text.
16. **`-safe` blocks system calls**: Shelling out via `system("magick ...")` triggers a fatal safety halt unless `-nosafe` is explicitly provided on the CLI.
17. **`labelpath` transform restrictions**: Only translation shifts on `Label` are respected: $x$-shift slides along the curve, $y$-shift offsets perpendicular to the curve. Rotations, scales, or slants inside the `Label` are silently discarded.

---

## Compile-gated examples

### 1. Complete Flowchart with Decision Branches & Labels
```asy
// Program: flowchart_gate.asy
import flowchart;

size(0, 250);

// Define nodes
block bIn   = rectangle(Label("Data Stream", magenta), "Read packet", (0, 4), paleyellow, paleblue, darkblue);
block bCond = diamond(Label("Valid?", red), (0, 2.5), palegreen, darkgreen);
block bProc = roundrectangle("Process packet", (-2, 1));
block bLog  = bevel("Log error", (2, 1));
block bExit = circle("Halt", (0, -0.5));

// Render node shapes
draw(bIn);
draw(bCond);
draw(bProc);
draw(bLog);
draw(bExit);

// Connect nodes inside deferred-draw closure
add(new void(picture pic, transform t) {
  blockconnector operator -- = blockconnector(pic, t);
  
  bIn -- Down -- Arrow -- bCond;
  bCond -- Label("OK", 0.5, NW) -- Left -- Down -- Arrow -- bProc;
  bCond -- Label("Err", 0.5, NE) -- Right -- Down -- Arrow -- bLog;
  bProc -- Down -- Right -- Arrow -- bExit;
  bLog  -- Down -- Left -- Arrow -- bExit;
});
```

### 2. Contour Plot with Per-Level Pens and Value Labels
```asy
// Program: contour_gate.asy
import contour;

size(150, 150);

real saddle(real x, real y) { return x^2 - 2*y^2; }

real[] levels = uniform(-2, 2, 7);

// Sequence idiom for dynamic pen generation
pen[] penList = sequence(new pen(int i) {
  real val = levels[i];
  if(val < 0) return blue + dashed;
  if(val == 0) return black + 1.2bp;
  return red + solid;
}, levels.length);

// Sequence idiom for dynamic label placement
Label[] labelList = sequence(new Label(int i) {
  return Label(format("$%+0.1f$", levels[i]), Relative(0.5), (0,0), UnFill(0.5bp));
}, levels.length);

draw(labelList, contour(saddle, (-2, -2), (2, 2), levels), penList);
```

### 3. Slope Field with Solution Curve & Interval Markers
```asy
// Program: slopefield_gate.asy
import slopefield;
import markers;

size(160, 160);

// ODE: dy/dx = x - y
real f(real x, real y) { return x - y; }

// Render vector field
add(slopefield(f, (-2, -2), (2, 2), 15, tickfactor=0.4, blue));

// Compute integral curve passing through (0, -1)
path sol = curve((0, -1), f, (-2, -2), (2, 2));
draw(sol, linewidth(1bp) + heavygreen);

// Mark points along the solution trajectory using stick marks
draw(sol, marker(stickframe(n=1, size=4, angle=90, p=red), markinterval(5, stickframe(1, 4, 90, red))));
```

### 4. Hatching and Custom Pattern Fills
```asy
// Program: patterns_gate.asy
import patterns;

size(180, 80);

// Register patterns into global dictionary
add("diagonal", hatch(H=3mm, dir=NE, p=darkblue));
add("cross", crosshatch(H=4mm, p=red));
add("bricks", brick(Hx=4mm, Hy=2mm, p=black));

// Apply patterns via pattern() pen constructor
filldraw(unitsquare, pattern("diagonal"), black);
filldraw(shift(1.5, 0) * unitsquare, pattern("cross"), black);
filldraw(shift(3.0, 0) * unitsquare, pattern("bricks"), black);
```
