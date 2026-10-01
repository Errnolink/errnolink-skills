# 2D Graphing, LaTeX Embedding & Palettes (Ch. 7, §8.1, §8.4–8.7, pp. 111–145, 164–171)
> Read this when drawing: 2D scientific function plots, data curves from arrays or CSV/files, custom/broken/secondary axes, error bars, vector fields, LaTeX document embedding (`asymptote.sty`), color density plots (`image`), and palette bars.

## Syntax recap
```asy
import graph;
size(8cm, 6cm, IgnoreAspect);

real f1(real x) { return sin(2pi*x); }
real f2(real x) { return cos(2pi*x); }

draw(graph(f1, 0, 1), red, "$\sin(2\pi x)$");
draw(graph(f2, 0, 1), blue+dashed, "$\cos(2\pi x)$");

xaxis("$x$", BottomTop, LeftTicks);
yaxis("$y$", LeftRight, RightTicks(trailingzero));

dot(Label("$(0.5, 0)$", align=NE), Scale((0.5, 0)));
add(legend(), point(E), 20E, UnFill);
```
- `import graph;` loads 2D plotting primitives, axis types, and coordinate scaling rules.
- `size(w, h, IgnoreAspect);` establishes non-uniform picture dimensions for scientific plots.
- `draw(graph(f, a, b), pen, "legend");` constructs sampled `guide`, draws curve, registers legend item.
- `xaxis(...)` and `yaxis(...)` draw bounding or centered axes with automatic/custom tick placement.
- `Scale((x, y))` translates raw data coordinates into post-scaled canvas coordinates for annotations.
- `add(legend(), point(E), 20E, UnFill);` positions legend frame outside the plot without overlapping.

## Vocabulary

### LaTeX Embedding (Ch. 7)
- `\usepackage{asymptote}` — loads Asymptote package in LaTeX; options: `inline`, `attach`.
- `\begin{asydef} ... \end{asydef}` — defines global Asymptote settings/preambles across figures; compiled once, draws nothing.
- `\begin{asy}[<keyvals>] ... \end{asy}` — embeds inline figure; `\end{asy}` MUST appear alone on its line with no leading/trailing characters.
- `\asyinclude[<keyvals>]{<filename.asy>}` — includes external `.asy` figure file; tracked automatically by `latexmk`.
- `void usepackage(string s)` — loads LaTeX package inside Asymptote figure environment (e.g. `usepackage("bm");`).
- `void texpreamble(string s)` — injects raw LaTeX code into the generated TeX preamble (e.g. `texpreamble("\def\V#1{\bm{#1}}");`).

### 2D Graph Constructors (§8.1)
- `guide graph(picture pic=currentpicture, real f(real), real a, real b, int n=ngraph, real T(real)=identity, interpolate join=operator --)` — graph of function $y=f(x)$ on $[T(a), T(b)]$.
- `guide[] graph(picture pic=currentpicture, real f(real), real a, real b, int n=ngraph, real T(real)=identity, bool3 cond(real), interpolate join=operator --)` — graph of function with branch-gap/discontinuity condition.
- `guide graph(picture pic=currentpicture, real x(real), real y(real), real a, real b, int n=ngraph, real T(real)=identity, interpolate join=operator --)` — parametric curve $(x(t), y(t))$ on $[T(a), T(b)]$.
- `guide[] graph(picture pic=currentpicture, real x(real), real y(real), real a, real b, int n=ngraph, real T(real)=identity, bool3 cond(real), interpolate join=operator --)` — parametric curve with branch condition.
- `guide graph(picture pic=currentpicture, pair z(real), real a, real b, int n=ngraph, real T(real)=identity, interpolate join=operator --)` — parametric curve $z(t)$ on $[T(a), T(b)]$.
- `guide[] graph(picture pic=currentpicture, pair z(real), real a, real b, int n=ngraph, real T(real)=identity, bool3 cond(real), interpolate join=operator --)` — parametric curve $z(t)$ with branch condition.
- `guide graph(picture pic=currentpicture, pair[] z, interpolate join=operator --)` — graph connecting array of coordinate pairs.
- `guide[] graph(picture pic=currentpicture, pair[] z, bool3[] cond, interpolate join=operator --)` — graph connecting array of pairs filtered by boolean condition array.
- `guide graph(picture pic=currentpicture, real[] x, real[] y, interpolate join=operator --)` — graph connecting coordinate arrays $(x, y)$.
- `guide[] graph(picture pic=currentpicture, real[] x, real[] y, bool3[] cond, interpolate join=operator --)` — graph connecting coordinate arrays filtered by boolean condition array.
- `guide polargraph(picture pic=currentpicture, real f(real), real a, real b, int n=ngraph, interpolate join=operator --)` — polar curve $r=f(\theta)$ on $[a, b]$.
- `guide polargraph(picture pic=currentpicture, real[] r, real[] theta, interpolate join=operator --)` — polar curve from array data.
- `guide graphwithderiv(pair f(real), pair fprime(real), real a, real b, int n=ngraph#10)` — parametric curve with exact Bezier tangents from derivative $f'$.

### Axes & Coordinate Lines (§8.1)
- `void xaxis(picture pic=currentpicture, Label L="", axis axis=YZero, real xmin=-infinity, real xmax=infinity, pen p=currentpen, ticks ticks=NoTicks, arrowbar arrow=None, bool above=false)` — draws horizontal x axis.
- `void yaxis(picture pic=currentpicture, Label L="", axis axis=XZero, real ymin=-infinity, real ymax=infinity, pen p=currentpen, ticks ticks=NoTicks, arrowbar arrow=None, bool above=false, bool autorotate=true)` — draws vertical y axis.
- `void axes(picture pic=currentpicture, Label xlabel="", Label ylabel="", bool extend=true, pair min=(-infinity,-infinity), pair max=(infinity,infinity), pen p=currentpen, arrowbar arrow=None, bool above=false)` — convenience routine drawing both x and y axes.
- `void xequals(picture pic=currentpicture, Label L="", real x, bool extend=false, real ymin=-infinity, real ymax=infinity, pen p=currentpen, ticks ticks=NoTicks, bool above=true, arrowbar arrow=None)` — vertical axis/line at $x$.
- `void yequals(picture pic=currentpicture, Label L="", real y, bool extend=false, real xmin=-infinity, real xmax=infinity, pen p=currentpen, ticks ticks=NoTicks, bool above=true, arrowbar arrow=None)` — horizontal axis/line at $y$.
- `void axis(picture pic=currentpicture, Label L="", path g, pen p=currentpen, ticks ticks, ticklocate locate, arrowbar arrow=None, int[] divisor=new int[], bool above=false, bool opposite=false)` — general axis along arbitrary path $g$.
- `ticklocate ticklocate(real a, real b, autoscaleT S=defaultS, real tickmin=-infinity, real tickmax=infinity, real time(real)=null, pair dir(real)=zero)` — tick locator for arbitrary path axis.

### Axis Placements (§8.1)
- `axis YZero(bool extend=true)` — x axis at $y=0$ (or $y=1$ on log axis); extends across plot unless `extend=false`.
- `axis YEquals(real Y, bool extend=true)` — x axis at specified coordinate $y=Y$.
- `axis Bottom(bool extend=false)` — x axis along bottom bounding box edge.
- `axis Top(bool extend=false)` — x axis along top bounding box edge.
- `axis BottomTop(bool extend=false)` — dual x axes along bottom and top bounding box edges.
- `axis XZero(bool extend=true)` — y axis at $x=0$ (or $x=1$ on log axis); extends across plot unless `extend=false`.
- `axis XEquals(real X, bool extend=true)` — y axis at specified coordinate $x=X$.
- `axis Left(bool extend=false)` — y axis along left bounding box edge.
- `axis Right(bool extend=false)` — y axis along right bounding box edge.
- `axis LeftRight(bool extend=false)` — dual y axes along left and right bounding box edges.

### Tick Constructors & Modifiers (§8.1)
- `ticks LeftTicks(Label format="", ticklabel ticklabel=null, bool beginlabel=true, bool endlabel=true, int N=0, int n=0, real Step=0, real step=0, bool begin=true, bool end=true, tickmodifier modify=None, real Size=0, real size=0, bool extend=false, pen pTick=nullpen, pen ptick=nullpen)` — ticks on left side of axis direction.
- `ticks RightTicks(...)` — ticks on right side of axis direction (identical parameters).
- `ticks Ticks(...)` — ticks on both sides of axis direction (identical parameters).
- `ticks LeftTicks(Label format="", ticklabel ticklabel=null, bool beginlabel=true, bool endlabel=true, real[] Ticks, real[] ticks=new real[], real Size=0, real size=0, bool extend=false, pen pTick=nullpen, pen ptick=nullpen)` — custom ticks at explicit coordinate locations.
- `ticks NoTicks` — suppress tick generation (default).
- `tickmodifier OmitTick(... real[] x)` — removes auto-generated ticks and labels at coordinates $x$.
- `tickmodifier OmitTickInterval(real a, real b)` — removes auto-generated ticks in interval $[a, b]$.
- `tickmodifier OmitTickIntervals(real[] a, real[] b)` — removes ticks across multiple intervals.
- `ticklabel OmitFormat(string s=defaultformat, ... real[] x)` — suppresses labels at coordinates $x$ while retaining tick marks.
- `tickmodifier NoZero` — abbreviation for `OmitTick(0)`.
- `ticklabel NoZeroFormat` — abbreviation for `OmitFormat(0)`.

### Manual Ticks & Coordinate Labels (§8.1)
- `void xtick(picture pic=currentpicture, Label L="", explicit pair z, pair dir=N, string format="", real size=Ticksize, pen p=currentpen)` — manual tick at pair $z$.
- `void xtick(picture pic=currentpicture, Label L="", real x, pair dir=N, string format="", real size=Ticksize, pen p=currentpen)` — manual tick at $x$.
- `void ytick(picture pic=currentpicture, Label L="", explicit pair z, pair dir=E, string format="", real size=Ticksize, pen p=currentpen)` — manual tick at pair $z$.
- `void ytick(picture pic=currentpicture, Label L="", real y, pair dir=E, string format="", real size=Ticksize, pen p=currentpen)` — manual tick at $y$.
- `void tick(picture pic=currentpicture, pair z, pair dir, real size=Ticksize, pen p=currentpen)` — single bare tick mark at $z$.
- `void labelx(picture pic=currentpicture, Label L="", explicit pair z, align align=S, string format="", pen p=currentpen)` — manual label at pair $z$.
- `void labelx(picture pic=currentpicture, Label L="", real x, align align=S, string format="", pen p=currentpen)` — manual label at coordinate $x$.
- `void labely(picture pic=currentpicture, Label L="", explicit pair z, align align=W, string format="", pen p=currentpen)` — manual label at pair $z$.
- `void labely(picture pic=currentpicture, Label L="", real y, align align=W, string format="", pen p=currentpen)` — manual label at coordinate $y$.

### Coordinate Scaling, Limits & Conversions (§8.1)
- `void scale(picture pic=currentpicture, scaleT x, scaleT y)` — sets axis scaling functions for picture.
- `void scale(picture pic=currentpicture, bool xautoscale=true, bool yautoscale=xautoscale, bool zautoscale=yautoscale)` — toggles automatic limit selection.
- `scaleT Linear(bool automin=false, bool automax=automin, real factor=1, real intercept=0)` — linear scaling; `factor=-1` reverses axis direction.
- `scaleT Log(bool automin=false, bool automax=automin)` — base-10 logarithmic scaling.
- `scaleT Broken(real a, real b)` — broken linear axis omitting segment $[a, b]$.
- `scaleT BrokenLog(real a, real b)` — broken logarithmic axis omitting $[a, b]$ (auto-rounded to powers of 10).
- `scaleT scaleT(real f(real), real finv(real), bool logarithmic=false)` — custom forward/inverse scale transform.
- `pair Scale(picture pic=currentpicture, pair z)` — converts raw data pair to post-scaled canvas coordinates.
- `real ScaleX(picture pic=currentpicture, real x)` — converts data $x$ to post-scaled canvas $x$.
- `real ScaleY(picture pic=currentpicture, real y)` — converts data $y$ to post-scaled canvas $y$.
- `void limits(picture pic=currentpicture, pair min, pair max, bool crop=NoCrop)` — sets explicit $(x, y)$ coordinate boundaries.
- `void xlimits(picture pic=currentpicture, real min=-infinity, real max=infinity, bool crop=NoCrop)` — sets horizontal limits.
- `void ylimits(picture pic=currentpicture, real min=-infinity, real max=infinity, bool crop=NoCrop)` — sets vertical limits.
- `void crop(picture pic=currentpicture)` — clips picture drawing to active graph limits.

### Secondary Axes (§8.1)
- `picture secondaryX(picture primary=currentpicture, void f(picture))` — creates secondary picture for upper horizontal axis.
- `picture secondaryY(picture primary=currentpicture, void f(picture))` — creates secondary picture for right vertical axis.

### Markers & Error Bars (§8.1)
- `marker marker(path g, markroutine markroutine=marknodes, pen p=currentpen, filltype filltype=NoFill, bool above=true)` — node marker from path $g$.
- `marker marker(frame f, markroutine markroutine=marknodes, bool above=true)` — node marker from frame $f$.
- `markroutine marknodes` — marker callback placing glyph at every Bezier node.
- `markroutine markuniform(bool centered=false, int n, bool rotated=false)` — marker callback placing $n$ marks evenly spaced by arclength.
- `markroutine markuniform(pair z(real t), real a, real b, int n)` — marker callback placing $n$ marks along parametric curve.
- `marker[] Mark` — predefined unfilled markers (`unitcircle`, `polygon(3..5)`, `cross(4,6)`, `diamond`, `plus`).
- `marker[] MarkFill` — predefined filled markers (`unitcircle`, `polygon(3..5)`, `diamond`).
- `void errorbars(picture pic=currentpicture, pair[] z, pair[] dp, pair[] dm={}, bool[] cond={}, pen p=currentpen, real size=0)` — error bars on coordinate pairs ($dp=$ positive error, $dm=$ negative error).
- `void errorbars(picture pic=currentpicture, real[] x, real[] y, real[] dpx, real[] dpy, real[] dmx={}, real[] dmy={}, bool[] cond={}, pen p=currentpen, real size=0)` — error bars on separate $x, y$ arrays.

### Legends (§8.1)
- `frame legend(picture pic=currentpicture, int perline=1, real xmargin=legendmargin, real ymargin=xmargin, real linelength=legendlinelength, real hskip=legendhskip, real vskip=legendvskip, pen p=currentpen)` — builds legend frame from curves with label strings.
- `void add(picture dest=currentpicture, frame src, pair position, pair align=0, filltype filltype=NoFill)` — adds legend to picture (e.g. `add(legend(), point(E), 20E, UnFill);`).
- `void attach(picture dest=currentpicture, frame src, pair position, pair align=0, filltype filltype=NoFill)` — attaches legend keeping main graph dimensions fixed.

### Vector Fields (§8.1)
- `picture vectorfield(path vector(real), path g, int n, bool truesize=false, pen p=currentpen, arrowbar arrow=Arrow)` — draws $n$ vector arrows along path $g$.
- `picture vectorfield(path vector(pair), pair a, pair b, int nx=nmesh, int ny=nx, bool truesize=false, real maxlength=truesize ? 0 : maxlength(a,b,nx,ny), bool cond(pair z)=null, pen p=currentpen, arrowbar arrow=Arrow, margin margin=PenMargin)` — draws grid of $nx \times ny$ vector arrows in box $[a, b]$.

### File & Table Input (§8.1)
- `file input(string name)` — opens file input stream.
- `file line(file f)` — configures file stream for newline-delimited record reading.
- `file csv(file f)` — configures file stream for comma-separated value reading.
- `real[][] transpose(real[][] a)` — transposes 2D matrix (turns file rows into column vectors).

### Palettes, Density Images & Color Bars (§8.4–8.7)
- `pen[] Grayscale(int NColors=256)` — grayscale palette.
- `pen[] Rainbow(int NColors=32766)` — full rainbow spectrum palette.
- `pen[] BWRainbow(int NColors=32761)` — rainbow spectrum tapering to black/white ends.
- `pen[] BWRainbow2(int NColors=32761)` — double rainbow palette with linear intensity tapering.
- `pen[] Wheel(int NColors=32766)` — full cyclical color wheel.
- `pen[] Gradient(int NColors=256, ... pen[] p)` — linear interpolation palette passing through given pens.
- `pen[] cmyk(pen[] Palette)` — converts palette pen array to CMYK colorspace.
- `bounds image(picture pic=currentpicture, real f(real, real), range range=Full, pair initial, pair final, int nx=ngraph, int ny=nx, pen[] palette, int divs=0, bool antialias=false)` — color density plot from function $f(x,y)$; returns `bounds`.
- `bounds image(picture pic=currentpicture, real[][] f, range range=Full, pair initial, pair final, pen[] palette, int divs=0, bool transpose=(initial.x < final.x && initial.y < final.y), bool copy=true, bool antialias=false)` — color density plot from 2D array; returns `bounds`.
- `bounds image(picture pic=currentpicture, pair[] z, real[] f, range range=Full, pen[] palette)` — density plot from irregular coordinate pairs.
- `bounds image(picture pic=currentpicture, real[] x, real[] y, real[] f, range range=Full, pen[] palette)` — density plot from irregular $x, y$ coordinates.
- `void image(picture pic=currentpicture, pen[][] data, pair initial, pair final, bool transpose=(initial.x < final.x && initial.y < final.y), bool copy=true, bool antialias=false)` — raster image from direct pen matrix.
- `void palette(picture pic=currentpicture, Label L="", bounds bounds, pair initial, pair final, axis axis=Right, pen[] palette, pen p=currentpen, paletteticks ticks=PaletteTicks, bool copy=true, bool antialias=false)` — draws labeled palette color bar over `bounds`.
- `paletteticks PaletteTicks(Label format="", ticklabel ticklabel=null, bool beginlabel=true, bool endlabel=true, int N=0, int n=0, real Step=0, real step=0, pen pTick=nullpen, pen ptick=nullpen)` — tick specifier for palette color bar.
- `pen[] palette(real[] f, pen[] palette)` / `pen[][] palette(real[][] f, pen[] palette)` — maps real values directly to pen arrays.

### Color Modules (§8.5–8.7)
- `import colormap;` — Matplotlib colormaps: `pen[] Palette = wistia.palette();` (also `viridis`, `plasma`, `magma`, `inferno`, etc.).
- `import texcolors;` — standard 68 CMYK TeX colors (`GreenYellow`, `Goldenrod`, `Melon`, `Mahogany`, ...).
- `import x11colors;` — standard 140 RGB X11 colors (`AliceBlue`, `Aquamarine`, `CadetBlue`, `Chartreuse`, ...).

## Options/parameters

### LaTeX asy / asyinclude Keyval Options
| Option | Type | Default | Description |
|---|---|---|---|
| `width` | TeX dimen | none | Forces figure width (e.g. `\the\linewidth`, `4cm`). |
| `height` | TeX dimen | none | Forces figure height (e.g. `3cm`). |
| `keepAspect` | bool | `true` | Preserves figure aspect ratio when scaling. |
| `viewportwidth` | TeX dimen | none | Sets 3D viewport bounding width. |
| `viewportheight` | TeX dimen | none | Sets 3D viewport bounding height. |
| `attach` | bool | `false` | Attaches 3D PRC file as embedded PDF attachment (`attachfile2`). |
| `inline` | bool | `false` | Generates inline LaTeX code instead of EPS/PDF; shares TeX symbols. |

### xaxis and yaxis Options
| Parameter | Default | Effect |
|---|---|---|
| `L` | `""` | Axis label string. Positioned at relative location 1 (`EndPoint`) by default. |
| `axis` | `YZero` / `XZero` | Placement type: `YZero`, `YEquals(y)`, `Bottom`, `Top`, `BottomTop`, `XZero`, `XEquals(x)`, `Left`, `Right`, `LeftRight`. |
| `xmin`, `xmax`, `ymin`, `ymax` | `±infinity` | Axis range limits. Infinite values automatically infer range from drawn objects. |
| `p` | `currentpen` | Pen used to draw axis line. |
| `ticks` | `NoTicks` | Tick specifier: `LeftTicks`, `RightTicks`, `Ticks`, `NoTicks`. |
| `arrow` | `None` | Arrowhead specification (e.g. `Arrow`, `Arrows`, `EndArrow`). |
| `above` | `false` | When `false`, axis is drawn underneath existing graphical objects. |
| `autorotate` | `true` | (`yaxis` only) Automatically rotates y-axis label to match axis direction. |

### LeftTicks / RightTicks / Ticks Options
| Parameter | Default | Effect |
|---|---|---|
| `format` | `""` (initially `"$%.4g$"`) | Label format string (C printf syntax). Use `trailingzero` for trailing zeros, `"%" ` to suppress text. |
| `ticklabel` | `null` | Custom callback `string(real x)` returning label for major tick at $x$. |
| `beginlabel`, `endlabel` | `true`, `true` | Include/suppress labels on the very first and last major ticks. |
| `N` | `0` | Number of major intervals when autoscaling. On log axis: decades between ticks. |
| `n` | `0` | Number of minor subintervals between each pair of major ticks. |
| `Step` | `0` | Explicit numerical value spacing between major ticks (overrides `N`). |
| `step` | `0` | Explicit numerical value spacing between minor ticks (overrides `n`). |
| `begin`, `end` | `true`, `true` | Include/suppress the first and last physical tick marks. |
| `modify` | `None` | Filter callback modifying `tickvalue` struct (e.g. `OmitTick(0)`, `NoZero`). |
| `Size`, `size` | `0`, `0` | Major and minor tick lengths in PostScript points. |
| `extend` | `false` | Extends ticks across the entire graph between opposite axes (draws grid). |
| `pTick`, `ptick` | `nullpen`, `nullpen` | Pens for major and minor tick lines. |
| `Ticks`, `ticks` | (array overload) | Explicit arrays of real coordinates for major and minor ticks. |

### Scales and Limits
| Routine | Arguments | Purpose |
|---|---|---|
| `Linear` | `automin=false, automax=automin, factor=1, intercept=0` | Linear scale; `factor=-1` reverses axis; `automin=true` picks "nice" round bounds. |
| `Log` | `automin=false, automax=automin` | Base-10 logarithmic scale; coordinates mapped to $\log_{10}(x)$. |
| `Broken` | `real a, real b` | Linear scale omitting interval $[a, b]$; tick labels break cleanly. |
| `BrokenLog` | `real a, real b` | Logarithmic scale omitting $[a, b]$ (endpoints snapped to integer powers). |
| `limits` | `pair min, pair max, bool crop=NoCrop` | Restricts axes box to $[min.x, max.x] \times [min.y, max.y]$. |
| `xlimits`, `ylimits`| `real min, real max, bool crop=NoCrop` | Restricts single axis range. Set `crop=Crop` to clip objects. |

### image() and palette() Parameters
| Parameter | Default | Effect |
|---|---|---|
| `range` | `Full` | Palette mapping range: `Full` (data min..max), `Automatic` (nice limits), `Range(min, max)`. |
| `nx`, `ny` | `ngraph`, `nx` | Grid sampling resolution for function density plots. |
| `divs` | `0` | Quantizes continuous palette into `divs - 1` discrete color intervals. |
| `transpose` | `initial.x < final.x && ...` | `true` interprets array Cartesian-style $(x, y)$; `false` matrix-style $(-y, x)$. |
| `antialias` | `false` | Bilinear interpolation of color cells during rendering. |
| `copy` | `true` | Clones array data buffer to prevent mutation. |
| `axis` | `Right` | (`palette` bar only) Tick side: `Right`, `Left`, `Top`, `Bottom`. |

## Traps

1. **Data coordinates vs. Canvas coordinates.**
   After `scale(Log, Log)` or any autoscaling, all paths, labels, and dot coordinates are stored in post-scaled canvas coordinates.
   - WRONG: `dot(Label("(3,5)"), (3,5));` — silently places dot at canvas coordinate $(3, 5)$ pt!
   - CORRECT: `dot(Label("(3,5)"), Scale((3,5)));`
   - In secondary picture $q$: use `Scale(q, (x, y))`.
2. **`bool3 cond` semantics in `graph()`.**
   - `true`: adds point to the current active curve guide.
   - `default`: starts a new separate guide starting at this point (creates discontinuous branch).
   - `false`: omits the point entirely AND starts a new guide at the subsequent valid point.
3. **Elementwise array operations & array filtering.**
   - `real[] y = x^2;` squares array elements elementwise.
   - Logical condition on arrays `t >= 10 & t <= 15` produces `bool[]` using single `&` (NOT `&&`).
   - Conditional array pruning: `t = rho > 0 ? t : null;` shrinks the array by deleting non-matching elements.
4. **Axis label default position is `EndPoint` (1.0).**
   `xaxis("$x$")` places "$x$" at relative coordinate 1 (far right), NOT at the center. To place at center or start: `xaxis(Label("$x$", position=0.5, align=S))` or `xaxis(Label("$x$", position=0))`.
5. **Axes drawn under objects by default.**
   `above=false` is default for `xaxis` and `yaxis`. Grid ticks or axes will be drawn beneath filled graphs or image density plots unless `above=true` is explicitly passed.
6. **Density plot `transpose` default.**
   If `initial` is left-and-below `final`, array indices default to Cartesian $(x, y)$. If corners are reversed, it silently switches to matrix convention $(-y, x)$, rotating the data 90 degrees.
7. **Palette bar binds to picture `z` scaling.**
   `bounds range = image(...)` captures the data range. A palette bar drawn with `palette(bar, ...)` uses the **z-axis scaling** of the picture (e.g. `scale(Linear, Linear, Log)`), NOT the x or y scaling.
8. **LaTeX `\end{asy}` must be completely isolated.**
   In LaTeX documents, `\end{asy}` MUST appear on a line by itself with no preceding or trailing spaces, comments, or commands. A blank line immediately after `\begin{asy}` is strictly forbidden.
9. **LaTeX `inline=true` zero-size symbols.**
   When using `\usepackage[inline]{asymptote}`, symbols defined outside the environment have zero bounding box size inside Asymptote. Pass size estimates explicitly: `draw(Label("$\macro$", "estimate"), box, z);`.
10. **`texcolors` vs `x11colors` namespace collisions.**
    Both define named pens, but definitions disagree (e.g. `Green`). Import only one, or let the latter import shadow the former deliberately.
11. **`import fontsize;` must be first.**
    When using nonstandard LaTeX font sizing via the `fontsize` module, `import fontsize;` must appear at the very top of the `.asy` file to load `type1cm` before text calls.
12. **`legend` and `vectorfield` use `add()` or `attach()`, NOT `draw()`.**
    `legend()` returns a `frame`; `vectorfield()` returns a `picture`. Passing them to `draw(...)` causes a compile-time type mismatch. Use `add(legend(), ...)` or `attach(legend(), ...)`.
13. **No postfix `++` or `--`.**
    Asymptote has NO postfix `i++` or `i--`. Always use prefix `++i`, `--i`, or `i += 1`.
14. **Division semantics.**
    `/` between integers returns a `real` (e.g. `3/2 == 1.5`). Integer division uses `#` (e.g. `3#2 == 1`).
15. **Pair multiplication `*` is complex multiplication.**
    `(a, b) * (c, d) == (a*c - b*d, a*d + b*c)`. For dot product, use `dot(u, v)`.

## Compile-gated examples

### Example 1: Scientific 2D function graph with dual axes & legend
```asy
import graph;
size(250, 180, IgnoreAspect);

real f(real x) { return exp(-x) * sin(2pi*x); }
real g(real x) { return exp(-x); }

draw(graph(f, 0, 3), red, "$e^{-x}\sin(2\pi x)$");
draw(graph(g, 0, 3), blue+dashed, "$e^{-x}$");

xaxis("$x$", BottomTop, LeftTicks);
yaxis("$y$", LeftRight, RightTicks(trailingzero));

yequals(0, Dotted);
add(legend(), point(E), 20E, UnFill);
```

### Example 2: Array data with custom markers, error bars & data annotation
```asy
import graph;
size(200, 150, IgnoreAspect);

real[] x = {10, 25, 45, 70, 90};
real[] y = {15, 38, 52, 68, 85};
real[] dy = {3, 5, 4, 6, 5};

errorbars(x, y, dy, red);
draw(graph(x, y), blue, marker(scale(1mm)*polygon(4), blue, FillDraw(white)));

scale(true);
xaxis("$T\ (\mathrm{K})$", BottomTop, LeftTicks);
yaxis("$R\ (\Omega)$", LeftRight, RightTicks);

dot(Label("Peak", align=NW), Scale((45, 52)));
```

### Example 3: Log-log plot with custom limits, grid & secondary axis
```asy
import graph;
size(220, 160, IgnoreAspect);

real amp(real w) { return 1.0 / sqrt(1 + w^2); }
real phase(real w) { return -atan(w) * (180 / pi); }

scale(Log, Log);
draw(graph(amp, 0.1, 100), blue);
xlimits(0.1, 100);
ylimits(0.005, 2);

pen gridpen = linewidth(0.5*linewidth()) + gray(0.8);
xaxis("$\omega$", BottomTop, LeftTicks(extend=true, ptick=gridpen));
yaxis("Gain $|G|$", Left, RightTicks(extend=true, ptick=gridpen));

picture sec = secondaryY(new void(picture pic) {
  scale(pic, Log, Linear);
  draw(pic, graph(pic, phase, 0.1, 100), red+dashed);
  ylimits(pic, -90, 0);
  yaxis(pic, "Phase ($^\circ$)", Right, red, LeftTicks(begin=false, end=false));
});
add(sec);
```

### Example 4: 2D color density plot with function, bounds & external palette bar
```asy
import graph;
import palette;
size(8cm, 8cm, IgnoreAspect);

real f(real x, real y) { return sin(x) * cos(y); }

pen[] pal = BWRainbow();
bounds b = image(f, (-pi, -pi), (pi, pi), nx=100, pal);

xaxis("$x$", BottomTop, LeftTicks, above=true);
yaxis("$y$", LeftRight, RightTicks, above=true);

picture bar;
palette(bar, "$f(x,y)$", b, (0, 0), (0.5cm, 8cm), Right, pal, PaletteTicks("$%+#0.1f$"));
add(bar.fit(), point(E), 25E);
```
