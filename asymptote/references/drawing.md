# Drawing, Paths & Primitives (§3–5, pp. 9–30)
> Read this when drawing: 2D primitives, paths, cubic splines, labels, arrows, fills, clipping, shading, canvas sizing.

### Execution Modes
- **Batch mode**: `asy -V test` compiles `test.asy`, generates `test.eps` (or `asy -V -f pdf test` for PDF), and opens viewer `-V`.
- **Interactive mode**: Run `asy` for REPL prompt. Enter drawing statements line by line. Type `erase;` to clear the canvas, `input test;` to load a script, or `quit;` to exit.

## Syntax recap
```asy
// Sizing: user coords scaled to bounding box; +0.5bp prevents stroke clipping
size(100.5, 100.5);                     // 0 = unconstrained axis; or unitsize(1cm)
draw((0,0)--(1,1), Arrow);              // -- straight segment, Arrowhead at end
draw((1,0){up}..{left}(0,1), red);      // .. cubic spline with {dir} tangents
draw((0,0)..controls (0,0.5) and (0.5,1)..(1,1), dashed); // explicit Bezier controls
path ring = unitcircle ^^ scale(0.5)*unitcircle;          // ^^ groups into path[]
filldraw(ring, evenodd+yellow, black);  // evenodd fill rule combined into pen
label("$A$", (0,0), SW);                // LaTeX math string, coordinate, compass align
label("Mid", (0,0)--(1,1), LeftSide);   // path-relative alignment
layer();                                // forces future draw calls on top of labels
```

## Vocabulary

### Picture Canvas & Sizing
- `void size(picture pic=currentpicture, real x, real y=x, bool keepAspect=pic.keepAspect);` — Scale picture to fit rectangle (0 = unconstrained).
- `void size(picture pic=currentpicture, real xsize, real ysize, pair min, pair max);` — Force user box(min,max) to fit (xsize,ysize).
- `void unitsize(picture pic=currentpicture, real x, real y=x);` — Set exact user coordinate units (overrides size).
- `void shipout(string prefix=defaultfilename, picture pic=currentpicture, orientation orientation=orientation, string format="", bool wait=false, bool view=true, string options="", string script="", light light=currentlight, projection P=currentprojection);` — Output picture to file.
- `void layer(picture pic=currentpicture);` — Start new drawing layer (draws over earlier labels/images).
- `void newpage(picture pic=currentpicture);` — Output page break.
- `void erase(picture pic=currentpicture);` — Clear contents of picture without resetting sizing.

### Core Drawing & Filling
- `void draw(picture pic=currentpicture, Label L="", path g, align align=NoAlign, pen p=currentpen, arrowbar arrow=None, arrowbar bar=None, margin margin=NoMargin, Label legend="", marker marker=nomarker);` — Draw stroked path with optional annotations.
- `void draw(pair origin, picture pic=currentpicture, Label L="", path g, align align=NoAlign, pen p=currentpen, arrowbar arrow=None, arrowbar bar=None, margin margin=NoMargin, Label legend="", marker marker=nomarker);` — Draw fixed-size path in PostScript units around user origin.
- `void fill(picture pic=currentpicture, path g, pen p=currentpen);` — Fill interior of cyclic path `g`.
- `void fill(pair origin, picture pic=currentpicture, path g, pen p=currentpen);` — Fill fixed-size path in PostScript units around user origin.
- `void filldraw(picture pic=currentpicture, path g, pen fillpen=currentpen, pen drawpen=currentpen);` — Fill interior and stroke boundary.
- `void filloutside(picture pic=currentpicture, path g, pen p=currentpen);` — Fill region outside `g` to picture bounds.
- `void unfill(picture pic=currentpicture, path g);` — Erase/clip interior of `g` via even-odd trick.
- `void clip(picture pic=currentpicture, path g, stroke=false, pen fillrule=currentpen);` — Clip canvas to interior of `g` (or stroke region if `stroke=true`).

### Dot Overloads
- `void dot(picture pic=currentpicture, pair z, pen p=currentpen, filltype filltype=dotfilltype);` — Dot at point `z` (diameter = linewidth × dotfactor).
- `void dot(frame f, pair z, pen p=currentpen, filltype filltype=dotfilltype);` — Dot on frame `f`.
- `void dot(picture pic=currentpicture, Label L, pair z, align align=NoAlign, string format=defaultformat, pen p=currentpen, filltype filltype=dotfilltype);` — Labeled dot; `format="$%.4g$"` formats coordinate if `L` is empty.
- `void dot(picture pic=currentpicture, Label[] L=new Label[], pair[] z, align align=NoAlign, string format=defaultformat, pen p=currentpen, filltype filltype=dotfilltype);` — Dot array for point array `z`.
- `void dot(picture pic=currentpicture, path[] g, pen p=currentpen, filltype filltype=dotfilltype);` — Dot at every node of path array.
- `void dot(picture pic=currentpicture, Label[] L=new Label[], explicit path g, align align=RightSide, string format=defaultformat, pen p=currentpen, filltype filltype=dotfilltype);` — Dot at every node of path `g` with default `RightSide` alignment.
- `void dot(picture pic=currentpicture, Label L, pen p=currentpen, filltype filltype=dotfilltype);` — Dot at position defined inside Label `L`.

### Gradient & Tensor Shading
- `void axialshade(picture pic=currentpicture, path g, bool stroke=false, pen pena, pair a, bool extenda=true, pen penb, pair b, bool extendb=true);` — Linear gradient along axis `a--b`.
- `void radialshade(picture pic=currentpicture, path g, bool stroke=false, pen pena, pair a, real ra, bool extenda=true, pen penb, pair b, real rb, bool extendb=true);` — Radial gradient between circles `(a,ra)` and `(b,rb)`.
- `void latticeshade(picture pic=currentpicture, path g, bool stroke=false, pen fillrule=currentpen, pen[][] p);` — 2D lattice gradient from grid of pens (same colorspace).
- `void gouraudshade(picture pic=currentpicture, path g, bool stroke=false, pen fillrule=currentpen, pen[] p, pair[] z, int[] edges);` — Gouraud triangular mesh shading.
- `void gouraudshade(picture pic=currentpicture, path g, bool stroke=false, pen fillrule=currentpen, pen[] p, int[] edges);` — Gouraud shading on successive nodes of `g`.
- `void tensorshade(picture pic=currentpicture, path[] g, bool stroke=false, pen fillrule=currentpen, pen[][] p, path[] b=g, pair[][] z=new pair[][]);` — Tensor product / Coons patch shading (empty `z` = Coons).
- `void tensorshade(picture pic=currentpicture, path g, bool stroke=false, pen fillrule=currentpen, pen[] p, path b=g, pair[] z=new pair[]);` — Single-patch tensor/Coons shading.
- `void draw(picture pic=currentpicture, pen fillrule=currentpen, path[] g, pen[] p);` — Smooth gradient blend between consecutive paths in `g`.
- `void functionshade(picture pic=currentpicture, path[] g, bool stroke=false, pen fillrule=currentpen, string shader);` — Procedural PostScript calculator shader string (PDF TeX engines only).

### Labels & Annotations
- `void label(picture pic=currentpicture, Label L, pair position, align align=NoAlign, pen p=currentpen, filltype filltype=NoFill);` — Typeset LaTeX label at point.
- `void label(picture pic=currentpicture, Label L, path g, align align=NoAlign, pen p=currentpen, filltype filltype=NoFill);` — Typeset label along path (default midpoint).
- `Label Label(string s="", pair position, align align=NoAlign, pen p=nullpen, embed embed=Rotate, filltype filltype=NoFill);` — Label constructor with position.
- `Label Label(string s="", align align=NoAlign, pen p=nullpen, embed embed=Rotate, filltype filltype=NoFill);` — Positionless Label constructor.
- `void arrow(picture pic=currentpicture, Label L="", pair b, pair dir, real length=arrowlength, align align=NoAlign, pen p=currentpen, arrowbar arrow=Arrow, margin margin=EndMargin);` — Fixed-size pointer arrow targeting `b` from `dir`.
- `string baseline(string s, string template="\strut");` — Equalize label vertical baselines to template.
- `string minipage(string s, width=100pt);` — Wrap text into a paragraph of given width.
- `frame pack(pair align=2S, ... object inset[]);` — Pack label/frame objects into single frame.
- `object draw(picture pic=currentpicture, Label L, envelope e, real xmargin=0, real ymargin=xmargin, pen p=currentpen, filltype filltype=NoFill, bool above=true);` — Draw bounding envelope (`box`, `roundbox`, `ellipse`) around label.
- `path[] texpath(Label L);` — Return outlined path array that TeX fills for label text.

### Image & External Inclusion
- `string graphic(string name, string options="");` — Return EPS graphic inclusion string for `label()`.
- `void image(picture pic=currentpicture, pen[][] data, pair initial, pair final, bool transpose=(initial.x < final.x && initial.y < final.y), bool copy=true, bool antialias=false);` — Render 2D pen matrix as raster image onto rectangle `box(initial, final)`.
- `void image(picture pic=currentpicture, pen f(int, int), int width, int height, pair initial, pair final, bool transpose=(initial.x < final.x && initial.y < final.y), bool antialias=false);` — Procedural raster image from pixel generator `f(x, y)`.

### Path Operators & Connectors
- `--` — Straight line connector between nodes.
- `..` — Cubic spline connector (MetaFont/Hobby algorithm).
- `::` — Inflection-free spline abbreviation (`..tension atleast 1 ..`).
- `---` — Near-straight line abbreviation (`..tension atleast infinity..`).
- `&` — Concatenate two paths (strips last node of first path; must coincide).
- `^^` — Group independent paths into `path[]` array (pen lifts; does not affect spline endpoints).
- `cycle` — Close path smoothly back to initial node (or straight line if after `--`).
- `{dir}` — Outgoing/incoming tangent angle pair specifier at node.
- `..controls c0 and c1..` — Explicit cubic Bezier control points.
- `..tension t ..` — Set tension (default 1, minimum 0.75; separate trailing number with space!).
- `..tension atleast t ..` — Inhibit inflection points with minimum tension.
- `{curl c}` — Endpoint curvature (0 = straight inflection, 1 = circular).

---

## Options/parameters

### Sizing Semantics
| Call | Effect |
|---|---|
| `size(w, h=w, keepAspect=true)` | Scale to bounding box `w × h`. If `w` or `h` is 0, that axis is unconstrained. |
| `size(0, 0)` | User coords are PostScript big points (1bp = 1/72 inch). `identity()` transform. |
| `unitsize(x, y=x)` | Set exact user units: 1 user unit in x = `x` bp, 1 in y = `y` bp. Overrides `size`. |
| `size(100.5, 100.5)` | **+0.5bp Rule**: Strokes have linewidth (default 0.5bp) centered on paths. Sizing `0.5bp` larger than coordinate bounding box keeps stroke outlines unclipped. |

### Arrow & Bar Options
- Constructor: `arrowbar Arrow(arrowhead arrowhead=DefaultHead, real size=0, real angle=arrowangle, filltype filltype=null, position position=EndPoint)`
- Arrow values: `None`, `Blank` (draws nothing at all), `BeginArrow`, `MidArrow`, `EndArrow` (= `Arrow`), `Arrows`.
- Curved-arc arrows: `BeginArcArrow`, `EndArcArrow` (= `ArcArrow`), `MidArcArrow`, `ArcArrows`.
- Arrowheads: `DefaultHead`, `SimpleHead`, `HookHead`, `TeXHead`.
- Arrowhead arguments: `size` (0 = `arrowsize(p)`), `angle` (degrees), `filltype` (`null`, `Fill`, `NoFill`, `Draw`), `position` (real parameter $t$ along path; invalid for `MidArrow`/`Arrows`).
- Bars (dimension lines): `None`, `BeginBar`, `EndBar` (= `Bar`), `Bars`. Optional real length argument: `Bars(5)` (default `barsize(pen)`). Always pair with `margin=NoMargin`.

### Margins
| Margin Specifier | Meaning |
|---|---|
| `NoMargin` | Draw full path length without shortening (default). |
| `BeginMargin`, `EndMargin` (= `Margin`), `Margins` | Shrink path ends by `labelmargin(p)`. |
| `Margin(b, e=b)` | Shrink by `b` and `e` multiples of `labelmargin(p)`. |
| `BeginPenMargin`, `EndPenMargin` (= `PenMargin`), `PenMargins` | Shrink path ends by pen linewidth units. |
| `PenMargin(b, e=b)` | Custom multiples of pen linewidth. |
| `DotMargin`, `BeginDotMargin`, `DotMargins` | `PenMargin(-0.5*dotfactor, 0.5*dotfactor)`: stops path flush with end dot boundary. |
| `TrueMargin(b, e=b)` | Shrink by explicit PostScript units (bp), independent of pen. |

### Fill Types (`filltype`)
| Value | Effect |
|---|---|
| `NoFill` | Do not fill background. |
| `Fill` / `Fill(xm, ym=xm, pen p=nullpen)` | Fill interior (uses drawing pen if `p=nullpen`). |
| `Draw` / `Draw(xm, ym=xm, pen p=nullpen)` | Stroke boundary only. |
| `FillDraw(xm, ym=xm, fillpen, drawpen)` | Fill interior and stroke boundary. |
| `UnFill(xm, ym=xm)` | Clip region and surrounding margin out of canvas. |
| `RadialShade(pen penc, pen penr)` | Radial gradient fill from center to border. |
| `RadialShadeDraw(xm, ym, penc, penr, drawpen)` | Radial gradient fill with stroked outline. |

### Label Alignments & Positioning
- Compass directions: `N`, `S`, `E`, `W`, `NE`, `NW`, `SE`, `SW`, `ENE`, `WNW`, etc., plus `up`, `down`, `right`, `left`. (Defined in `plain`; prefix `plain.E` if shadowed).
- Offset distance: displaced by `align * labelmargin(p)` PostScript units.
- `NoAlign`: Center label bounding box directly on coordinate point.
- `Align`: Pin label's **bottom-left corner** directly at coordinate point.
- Path-relative directions: `LeftSide = Relative(W)`, `RightSide = Relative(E)`, `Center = Relative((0,0))`. Scale factor moves further: `2*LeftSide`.
- Path positions: real parameter $t$ (`point(g, t)`) or arclength fraction `Relative(real)`:
  - `BeginPoint = Relative(0)`
  - `MidPoint = Relative(0.5)`
  - `EndPoint = Relative(1)`
- `embed` modes: `Shift`, `Rotate` (default; tracks rotation), `Rotate(pair z)`, `Slant`, `Scale` (scales text with picture).

### Pen Attributes for Drawing
- Combining pens with `+`: Colors add together (`red + green == yellow`), but for all other attributes (linewidth, linetype, fillrule, font), the **rightmost** pen attribute overrides the leftmost.
- Dash styles: `solid`, `dotted`, `dashed`, `longdashed`, `dashdotted`, `longdashdotted`, `Dotted`, `linetype(new real[] {8, 8})`.
- Fill rules: `zerowinding` (default: non-zero winding number), `evenodd` (alternating parity). Combined into pen: `evenodd + yellow`.
- Baseline alignment: `basealign` (aligns LaTeX font baselines), `nobasealign` (default: aligns full bounding box).
- Overwrite collision control: `Allow` (default), `Suppress`, `SuppressQuiet`, `Move`, `MoveQuiet`.
- Font settings: `fontsize(real size, real lineskip=1.2*size)`, `font(string name, real size=0)`.
### Graphic Options
Options string for `graphic("file.eps", options)`:
- `width=val`, `height=val`, `angle=deg`, `scale=factor`, `clip=bool`, `draft=bool`, `bb=llx lly urx ury`.

---

## Traps

1. **`bp` vs `pt` coordinate scale**: 1bp = 1/72 inch (PostScript big points, bare numbers in Asymptote). 1pt = 1/72.27 inch (TeX points, used by `fontsize()`). Default stroke width is 0.5bp.
2. **Space before `..` after numbers**: In `tension 2 ..`, a space before `..` is mandatory. `2..` lexes as the real literal `2.` followed by `.`, causing a syntax error.
3. **`...` is NOT the MetaPost connector**: In Asymptote, `...` denotes variable rest-arguments. The MetaPost inflection-free curve connector is `::`.
4. **Postfix `++` and `--` do not exist**: Only prefix `++i` and `--i` are valid. Writing `i++` throws a compile error.
5. **Division `/` yields `real`, not `int`**: `3/2` evaluates to `1.5`, not `1`. For integer quotient, use `#`: `3#2 == 1`.
6. **`pair * pair` is complex multiplication**: Multiplying two pairs `(x1,y1) * (x2,y2)` computes `(x1*x2 - y1*y2, x1*y2 + y1*x2)`. For vector dot product, use `dot(z1, z2)`.
7. **Guide vs Path**: A `guide` delays spline solving until draw/cast time; a `path` is an already solved cubic spline. Concatenating guides with `..` solves globally; concatenating paths joins pre-solved curves.
8. **Transform order is right-to-left**: `rotate(45) * xscale(2) * L` scales x first, then rotates 45 degrees.
9. **Labels float above PostScript drawing objects**: Within a layer, all `label()`, `graphic()`, and verbatim TeX objects paint *on top of* `draw()` and `fill()` objects regardless of statement order. Call `layer();` to start a new plane when lines must cover labels/images.
10. **`clip()` transcends layers and pages**: `clip(pic, g)` clips the entire canvas across all layers and pages. To isolate clipping, draw into a local picture (`picture opic;`), clip `opic`, and paste via `add(pic, opic);`.
11. **Fill rule orientation trap with `^^`**: Under default `zerowinding`, filling `unitcircle ^^ scale(0.5)*unitcircle` fills both circles solid. To punch a hole, paths must have opposite direction: `unitcircle ^^ reverse(scale(0.5)*unitcircle)`, or combine `evenodd` into the pen: `evenodd + yellow`.
12. **Fill rule is a pen attribute**: The `fillrule` argument in `clip`, `fill`, and `latticeshade` is typed `pen`. Write `evenodd`, not an integer.
13. **`Align` vs `NoAlign`**: Default `NoAlign` centers the label bbox on the coordinate. `Align` pins the label's bottom-left corner at the coordinate.
14. **Pen inside `Label` overrides draw pen**: A pen passed into `Label("txt", p=red)` overrides any pen argument passed to `draw()` or `label()`.
15. **`Blank` arrow suppresses the path**: `arrow=Blank` draws neither arrowhead nor line segment.
16. **Bars demand `NoMargin`**: Dimension bars specify precise endpoints; applying margins shortens the path and detaches bars from target points.
17. **`{dir}` on endpoints**: Direction tangents on endpoints can only face the curve interior (e.g. `(0,0){up}..(1,1)` is legal; `{down}` pointing away from segment start is invalid).
18. **Tension lower bound is 0.75**: Real values passed to `tension` below 0.75 violate Hobby's algorithm bound and produce errors.
19. **Compass names are variables**: Declaring `int E;` or `pair N;` shadows compass directions. Recover with `plain.E` or `plain.N`.
20. **`&` strips the first path's last node**: `g1 & g2` assumes `point(g1, length(g1)) == point(g2, 0)`. If endpoints do not match, the second path leaps to the start without connecting cleanly.
21. **Pen attribute precedence**: Adding pens `p1 + p2` adds color channels, but non-color attributes (linewidth, dash style, fill rule, font) obey **rightmost overrides leftmost**: `dashed + solid` produces `solid`.
22. **Deferred drawing for connectors/blocks**: In flowcharts and diagrams where node sizes depend on LaTeX label dimensions, connector paths cannot be computed immediately. Asymptote resolves them via deferred drawing callbacks (`pic.add(new void(frame f, transform t) { ... })`), ensuring arrows connect to final label boundaries after scaling.
23. **Label direction argument types**: The alignment argument accepts either a fixed compass direction `pair` (`N`, `SW`), the constant `Align` (pinning bottom-left corner), or a tangent-relative direction `Relative(pair)` (`LeftSide`, `RightSide`). Passing bare angles in degrees directly without `dir(deg)` is invalid.

---

## Compile-gated examples

### Example 1: Precision Paths, Arrows, Bars & Splines
```asy
// Precision dimensioning and cubic splines
size(120.5, 80.5);

// Straight path with custom TeX-style arrows and pen margin
draw((10,20)--(110,20), arrow=Arrows(TeXHead), p=blue+1bp);

// Dimension bar marking distance with NoMargin
draw((10,10)--(110,10), bar=Bars, p=black+0.5bp);
label("100 bp", (60,10), S);

// Smooth Bezier spline with explicit tangent angles and tension
draw((10,30){up}..tension 1.5 ..(60,70){right}::(110,30){down}, 
     p=red+dashed, arrow=Arrow(HookHead, size=2mm));

// Labeled reference points
dot("$A$", (10,20), SW);
dot("$B$", (110,20), SE);
```

### Example 2: Cyclic Paths, Ring Hole Punching & Pen Rules
```asy
// Demonstrating ^^ grouping, evenodd rule vs reversed orientation
size(100, 100);

path outerBox = box((-2,-2), (2,2));
path innerCirc = scale(1.2)*unitcircle;

// Hole punched using evenodd pen attribute
filldraw(outerBox ^^ innerCirc, evenodd+lightgray, black+0.5bp);

// Nested shape punched via reversed path with default zerowinding
path subHole = scale(0.4)*unitsquare;
filldraw(scale(0.8)*unitcircle ^^ reverse(subHole), cyan, darkblue+0.5bp);
```

### Example 3: Path-Relative Labels, Alignment & Local Clipping
```asy
// Path-relative annotation and isolated picture clipping
unitsize(1cm);

path sCurve = (0,0){up}..(2,2){right}..(4,0){down};
draw(sCurve, linewidth(0.8bp));

// Label anchored along path arclength with LeftSide tangent offset
label(Label("Peak", MidPoint), sCurve, 1.5*LeftSide, p=darkgreen);
label(Label("Start", BeginPoint), sCurve, S);
label(Label("End", EndPoint), sCurve, S);

// Isolated picture clipping to prevent canvas-wide clip leakage
picture subpic;
unitsize(subpic, 1cm);
fill(subpic, box((0,-1), (2,1)), yellow);
draw(subpic, (0,-1)--(2,1), red+1bp);
clip(subpic, unitcircle); // clipped only within subpic bounds
add(currentpicture, subpic, (1, -1.5));
```

### Example 4: Axial Gradient Shading and Layered Graphic
```asy
// Axial gradient shading with subsequent layered annotations
size(80, 80);

path shield = (0,0)--(2,0)--(2.5,2)--(0.5,3)--cycle;

// Axial shading from white to deep blue across diagonal
axialshade(shield, stroke=false, white, (0,0), false, heavyblue, (2.5,3), false);
draw(shield, black+0.5bp);

// Typeset LaTeX label
label("\textbf{Shield}", (1.25, 1.2), p=white);

// Force next vector marks above any labels via layer()
layer();
draw((0.5, 0.5)--(2, 2.5), p=yellow+1.5bp, arrow=Arrow);
```
