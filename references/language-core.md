# Language: Core Types, Paths & Canvas (§6.1–§6.6, pp. 25–58)
> Read this when drawing: primitive data types (int, real, bool3, pair, triple, string), complex pair algebra, path and guide construction and queries, pen attributes and color mixing, affine transforms, frames vs pictures, canvas sizing and scaling models, deferred drawing.

## Syntax recap
```asy
// Implicit initializers: zeroed/identity, never uninitialized
int count; real val; pair z; path p; guide g; pen q; transform t; frame f;

// Pair as complex number: * is complex multiplication, not elementwise
pair a = (3, 4), b = 1 + 2*I; // I == (0, 1)
pair c = a * b;               // (-5, 10) = (3+4i)*(1+2i); use realmult(a,b) for (3,8)

// Incremental guide idiom: linear-time O(n) loop accumulation (path is O(n^2))
guide curve;
for (int i = 0; i <= 10; ++i) { curve = curve .. (i, sin(i)); }
path solved = curve;          // resolved to cubic spline once

// Pen algebra: + mixes colors additively; rightmost non-color attribute wins
pen mypen = red + dashed;
pen yellow_pen = mypen + green;        // red + green = yellow!
pen clean_blue = colorless(mypen) + blue; // colorless() prevents color mixing
pen thick = mypen + 2.0;               // pen + real sets linewidth(2.0), not added

// Affine transform: rightmost factor applies FIRST to object
transform T = rotate(45) * xscale(2);  // scales x by 2, then rotates 45 deg
pair w = T * (1, 0);

// Picture scaling & deferred drawing: t computed at shipout
picture pic;
unitsize(pic, 1cm);                    // overrides size(); 1 user unit = 1cm
pic.add(new void(frame f, transform t) {
  fill(f, circle(t * (1, 1), 2pt), red); // fixed 2pt dot at user (1,1)
});
pic.addPoint((1, 1), (-2pt, -2pt));   // register truesize extents for autoscaling
pic.addPoint((1, 1), (2pt, 2pt));
```

## Vocabulary

### Primitive & Numeric Data Types (§6.1, §6.10)
| Type | Default Initializer | Meaning & Range |
| :--- | :--- | :--- |
| `void` | *none* | Return type for functions returning no value |
| `bool` | `false` | Boolean `true` or `false` |
| `bool3` | `default` | Three-state boolean: `true`, `default`, `false` (casts to/from `bool`) |
| `int` | `0` | Signed integer: `intMin` to `intMax` |
| `real` | `0.0` | Native float: precision `realEpsilon`, `realDigits` sig digits; `realMin` to `realMax` |
| `pair` | `(0.0, 0.0)` | Ordered pair $(x, y)$, complex number $x + iy$; virtual members `z.x`, `z.y` (read-only) |
| `triple` | `(0.0, 0.0, 0.0)` | Ordered 3D triple $(x, y, z)$; virtual members `v.x`, `v.y`, `v.z` (read-only); origin `O` |
| `string` | `""` | Character string (STL string); indexed `s[pos]`, out-of-range returns `""` |
| `path` | `nullpath` | Resolved cubic spline; empty path |
| `guide` | `nullpath` | Unresolved cubic spline knot list; resolves at draw/cast time |
| `pen` | `defaultpen` | Drawing context (black, 0.5 bp line width, round cap/join, 12pt font) |
| `transform` | `identity()` | 6-component 2D affine transform matrix |
| `frame` | `newframe` | PostScript-coordinate canvas (absolute bp) |
| `picture` | empty picture | User-coordinate canvas (autoscaled to PostScript frame on shipout) |

- `intMin`, `intMax` — Smallest and largest allowed integer values.
- `realEpsilon`, `realDigits`, `realMin`, `realMax` — Float machine epsilon, precision digits, min/max positive values.
- `inf`, `nan` — IEEE floating-point infinity and not-a-number constants.
- `bool isnan(real x)` — Returns `true` if `x` is NaN.
- `pi` — Constant $\pi \approx 3.141592653589793$.
- `I` — Imaginary unit pair `(0, 1)`.
- `O` — 3D origin triple `(0, 0, 0)`.
- `int quotient(int x, int y)` — Returns $\lfloor x / y \rfloor$ (integer division).
- `a # b` — Integer division operator equivalent to `quotient(a, b)`.
- `a / b` — Division; promotes integer operands to `real`, returning a `real` quotient.
- `a % b` — Modulo operator; result has same sign as divisor $b$ ($q \cdot (p \# q) + p \% q == p$).
- `a ^ b`, `a ** b` — Power operator; recursive multiplication if $b$ is `int`, else log/exp.
- `++i`, `--i` — Prefix increment and decrement. Postfix `i++` and `i--` DO NOT EXIST.
- `int AND(int, int)`, `int OR(int, int)`, `int XOR(int, int)`, `int NOT(int)` — Bitwise operations.
- `int CLZ(int)`, `int CTZ(int)`, `int popcount(int)`, `int bitreverse(int a, int bits)` — Bit manipulation functions.

### Pair & Complex Algebra (§6.1)
- `pair conj(pair z)` — Complex conjugate $(z.x, -z.y)$.
- `real length(pair z)` / `real abs(pair z)` — Complex modulus $|z| = \sqrt{z.x^2 + z.y^2}$.
- `real abs2(pair z)` — Squared complex modulus $|z|^2 = z.x^2 + z.y^2$.
- `real angle(pair z, bool warn=true)` — Angle in radians in $[-\pi, \pi]$; returns 0 if $z=(0,0)$ and `warn=false`, errors if `warn=true`.
- `real degrees(pair z, bool warn=true)` — Angle in degrees in $[0, 360)$; returns 0 if $z=(0,0)$ and `warn=false`, errors if `warn=true`.
- `pair unit(pair z)` — Unit vector $z / |z|$ in direction of $z$.
- `pair expi(real angle)` — Unit vector $(\cos \theta, \sin \theta)$ from angle in radians.
- `pair dir(real degrees)` — Unit vector $(\cos \theta, \sin \theta)$ from angle in degrees.
- `real xpart(pair z)` — Returns $z.x$.
- `real ypart(pair z)` — Returns $z.y$.
- `pair realmult(pair z, pair w)` — Elementwise product $(z.x \cdot w.x, z.y \cdot w.y)$; NOT complex multiplication.
- `real dot(explicit pair z, explicit pair w)` — Scalar dot product $z.x \cdot w.x + z.y \cdot w.y$.
- `real cross(explicit pair z, explicit pair w)` — 2D scalar cross product $z.x \cdot w.y - z.y \cdot w.x$.
- `real orient(pair a, pair b, pair c)` — Positive if $a$--$b$--$c$--`cycle` is counterclockwise, negative if clockwise, 0 if collinear ($=\text{cross}(a-c, b-c)$).
- `real incircle(pair a, pair b, pair c, pair d)` — Positive if $d$ lies strictly inside circumcircle of CCW triangle $abc$, negative if outside, 0 on circle.
- `pair minbound(pair z, pair w)` — Componentwise minimum $(\min(z.x, w.x), \min(z.y, w.y))$.
- `pair maxbound(pair z, pair w)` — Componentwise maximum $(\max(z.x, w.x), \max(z.y, w.y))$.

### Triple Functions (§6.1)
- `real length(triple v)` / `real abs(triple v)` — Vector magnitude $|v|$.
- `real abs2(triple v)` — Squared magnitude $|v|^2$.
- `real polar(triple v, bool warn=true)` — Colatitude from $+z$ axis in radians $[0, \pi]$.
- `real azimuth(triple v, bool warn=true)` — Longitude from $+x$ axis in radians $[-\pi, \pi]$.
- `real colatitude(triple v, bool warn=true)` — Colatitude from $+z$ axis in degrees $[0, 180]$.
- `real latitude(triple v, bool warn=true)` — Latitude from $xy$ plane in degrees $[-90, 90]$.
- `real longitude(triple v, bool warn=true)` — Longitude from $+x$ axis in degrees $[0, 360)$.
- `triple unit(triple v)` — Unit vector $v / |v|$.
- `triple expi(real polar, real azimuth)` — Unit triple from radians.
- `triple dir(real colatitude, real longitude)` — Unit triple from degrees.
- `real xpart(triple v)`, `real ypart(triple v)`, `real zpart(triple v)` — Cartesian components.
- `real dot(triple u, triple v)` — 3D scalar dot product.
- `triple cross(triple u, triple v)` — 3D vector cross product $u \times v$.
- `triple minbound(triple u, triple v)`, `triple maxbound(triple u, triple v)` — Componentwise min/max.

### String Functions (§6.1)
- `int length(string s)` — Number of characters in `s`.
- `int find(string s, string t, int pos=0)` — Index of first occurrence of `t` at/after `pos`, or $-1$.
- `int rfind(string s, string t, int pos=-1)` — Index of last occurrence of `t` at/before `pos`, or $-1$.
- `string insert(string s, int pos, string t)` — String formed by inserting `t` at `pos`.
- `string erase(string s, int pos, int n)` — Erases $n$ characters at `pos` (if $n=-1$, to end of `s`).
- `string substr(string s, int pos, int n=-1)` — Substring of length $n$ starting at `pos`.
- `string reverse(string s)` — Reversed string.
- `string replace(string s, string before, string after)` — Replaces all occurrences of `before` with `after`.
- `string replace(string s, string[][] table)` — Replaces occurrences matching `{before, after}` pairs.
- `string[] split(string s, string delimiter="")` — Splits string by delimiter (empty delimiter splits on whitespace, collapsing duplicates).
- `string[] array(string s)` — Splits string into 1-character array; inverse is `operator +(...string[] a)`.
- `string format(string s, int n, string locale="")` — Formats integer using C `fprintf` specifier.
- `string format(string s=defaultformat, bool forcemath=false, string sep=defaultseparator, real x, string locale="")` — Formats real number; strips trailing zeros unless `#` specified; uses TeX math mode scientific notation.
- `int hex(string s)` — Parses hexadecimal string to integer.
- `int ascii(string s)` — ASCII value of first character.
- `string string(real x, int digits=realDigits)` — Converts float to string.
- `string time(string format="%a %b %d %T %Z %Y")` — Current time formatted via ANSI C `strftime`.
- `int seconds(string t="", string format="")` — Parses time to seconds since Epoch via `strptime`; returns $-1$ on error.
- `void abort(string s="")`, `void assert(bool b, string s="")`, `void exit()` — Execution control.

### Path & Guide Constructors & Connectors (§6.2, Ch. 5)
- `--` — Straight line connector.
- `..` — Cubic spline connector with automatic curvature matching.
- `::` — Cubic spline with `..tension atleast 1..` (prevents inflection loops).
- `---` — Cubic spline with `..tension atleast infinity..` (straight connector with tangent matching).
- `&` — Concatenates two paths/guides, stripping the redundant shared junction node.
- `cycle` — Closes a path or guide smoothly.
- `..controls c1 and c2..` — Explicit cubic Bézier control points between nodes.
- `..tension t1 and t2..` — Specifies knot tension (minimum 0.75, default 1).
- `..tension atleast t..` — MetaPost-style tension lower bound.
- `..{dir}..` — Specifies incoming/outgoing tangent direction pair at node.
- `{curl c}` — Boundary condition curl ratio (0 = straight/inflection, 1 = circular curvature, default 1).
- `path circle(pair c, real r)` — 4-segment cubic Bézier circle approximation centered at `c` with radius `r`.
- `path arc(pair c, real r, real angle1, real angle2)` — Arc centered at `c`, radius `r`, CCW if `angle2 >= angle1`.
- `path arc(pair c, real r, real angle1, real angle2, bool direction)` — Arc with explicit `CCW` (`true`) or `CW` (`false`).
- `path arc(pair c, explicit pair z1, explicit pair z2, bool direction=CCW)` — Arc from `z1` to `z2` around `c`.
- `path ellipse(pair c, real a, real b)` — Bézier ellipse centered at `c` with semi-axes `a` and `b`.
- `path brace(pair a, pair b, real amplitude=bracedefaultratio*length(b-a))` — Curly brace between `a` and `b`.

### Path Queries (~50 functions) (§6.2)
#### Topology & Segments
- `int length(path p)` — Number of cubic/linear segments in `p`. For cyclic paths, equals node count.
- `int size(path p)` — Number of nodes in `p`. For non-cyclic paths, equals `length(p) + 1`.
- `bool cyclic(path p)` — Returns `true` iff `p` is a closed cycle.
- `bool straight(path p, int i)` — Returns `true` iff segment between node $i$ and $i+1$ is a straight line.
- `bool piecewisestraight(path p)` — Returns `true` iff all segments are straight lines.
- `path reverse(path p)` — Returns path traversed in opposite direction.
- `path subpath(path p, int a, int b)` — Subpath between node indices $a$ and $b$; reversed if $a > b$.
- `path subpath(path p, real a, real b)` — Subpath between spline times $a$ and $b$; reversed if $a > b$.

#### Points & Bézier Controls
- `pair point(path p, int t)` — Coordinates of node $t$; clamps to endpoints if non-cyclic; mod `length(p)` if cyclic.
- `pair point(path p, real t)` — Point on spline at parameter $t$ ($\lfloor t \rfloor$ is segment, $t - \lfloor t \rfloor$ is Bézier parameter).
- `pair precontrol(path p, int t)` — Incoming cubic control point at node $t$.
- `pair precontrol(path p, real t)` — Effective incoming control point at parameter $t$.
- `pair postcontrol(path p, int t)` — Outgoing cubic control point at node $t$.
- `pair postcontrol(path p, real t)` — Effective outgoing control point at parameter $t$.

#### Tangents, Acceleration & Curvature
- `pair dir(path p, int t, int sign=0, bool normalize=true)` — Unit tangent at node $t$: `sign < 0` incoming, `sign > 0` outgoing, `sign == 0` mean.
- `pair dir(path p, real t, bool normalize=true)` — Unit tangent vector at spline parameter $t$.
- `pair dir(path p)` — Tangent at path end: equivalent to `dir(p, length(p))`.
- `pair dir(path p, path q)` — Average unit direction `unit(dir(p) + dir(q))` between two paths.
- `pair accel(path p, int t, int sign=0)` — Second derivative / acceleration vector at node $t$.
- `pair accel(path p, real t)` — Acceleration vector at spline parameter $t$.
- `real radius(path p, real t)` — Radius of curvature at spline parameter $t$.

#### Arclength & Time Parameter Conversions
- `real arclength(path p)` — Total length of curve in user coordinates.
- `real arctime(path p, real L)` — Spline time parameter $t \in [0, \text{length}(p)]$ where cumulative arclength equals $L$.
- `pair arcpoint(path p, real L)` — Point at cumulative arclength $L$; equivalent to `point(p, arctime(p, L))`.
- `real dirtime(path p, pair z)` — First spline time where curve tangent matches direction `z`, or $-1$ if none.
- `real reltime(path p, real l)` — Spline time at relative fraction $l \in [0, 1]$ of total arclength.
- `pair relpoint(path p, real l)` — Point at relative fraction $l \in [0, 1]$ of total arclength.
- `pair midpoint(path p)` — Point at half total arclength (`relpoint(p, 0.5)`).

#### Intersections & Slices
- `real[] intersect(path p, path q, real fuzz=-1)` — Returns array of 2 times `{tp, tq}` for first intersection, or `real[0]` (empty array) if none.
- `real[][] intersections(path p, path q, real fuzz=-1)` — Sorted array of all `{tp, tq}` intersection time pairs.
- `real[] intersections(path p, explicit pair a, explicit pair b, real fuzz=-1)` — Sorted intersection times of `p` with infinite line through `a` and `b`.
- `real[] times(path p, real x)` — All intersection times of `p` with vertical line through $(x, 0)$.
- `real[] times(path p, explicit pair z)` — All intersection times of `p` with horizontal line through $(0, z.y)$.
- `real[] mintimes(path p)` — Length-2 array of times where `p` reaches its minimal $x$ and $y$ extents.
- `real[] maxtimes(path p)` — Length-2 array of times where `p` reaches its maximal $x$ and $y$ extents.
- `pair intersectionpoint(path p, path q, real fuzz=-1)` — Point `point(p, intersect(p, q, fuzz)[0])`.
- `pair[] intersectionpoints(path p, path q, real fuzz=-1)` — Array of all intersection points.
- `pair extension(pair P, pair Q, pair p, pair q)` — Intersection point of extended lines $PQ$ and $pq$, or `(infinity, infinity)` if parallel.
- `slice cut(path p, path knife, int n)` — Cuts `p` at $n$-th intersection with `knife`; returns `struct slice { path before, after; }`.
- `slice firstcut(path p, path knife)` — Cut at first intersection (`cut(p, knife, 0)`).
- `slice lastcut(path p, path knife)` — Cut at last intersection (`cut(p, knife, -1)`).
- `path buildcycle(...path[] p)` — MetaPost `buildcycle` boundary path from 2+ consecutively intersecting paths.

#### Bounding Box & Region Containment
- `pair min(path p)` — Lower-left corner `(left, bottom)` of path bounding box.
- `pair max(path p)` — Upper-right corner `(right, top)` of path bounding box.
- `int windingnumber(path p, pair z)` — Winding number of cyclic `p` around `z` (positive = CCW; returns `undefined` if $z$ lies on `p`).
- `bool interior(int windingnumber, pen fillrule)` — `true` if winding number is inside under `fillrule`.
- `bool inside(path p, pair z, pen fillrule=currentpen)` — `true` iff point $z$ is inside or on boundary of cyclic `p`.
- `int inside(path p, path q, pen fillrule=currentpen)` — Returns `1` if cyclic `p` strictly contains `q`, `-1` if `q` contains `p`, `0` otherwise.
- `pair inside(path p, pen fillrule=currentpen)` — Returns an arbitrary point strictly inside cyclic `p`.
- `path[] strokepath(path g, pen p=currentpen)` — Outline paths that PostScript fills when stroking `g` with pen `p`.

### Guide-Specific Queries (§6.2)
- `int size(guide g)` — Number of unresolved nodes.
- `int length(guide g)` — Number of segments in guide.
- `bool cyclic(guide g)` — `true` iff guide has a `cycle` specifier.
- `pair point(guide g, int t)` — Node coordinates at index $t$ (no `real t` form for guides).
- `guide reverse(guide g)` — Reverses node order without solving cubic splines (except internal cycles).
- `pair[] dirSpecifier(guide g, int i)` — Length-2 array `{outgoing, incoming}` direction specifiers at segment $i$ (or `(0,0)` if unspecified).
- `pair[] controlSpecifier(guide g, int i)` — Length-2 array of explicit control points, or empty if none.
- `tensionSpecifier tensionSpecifier(guide g, int i)` — Tension object with virtual members `.in`, `.out`, `.atLeast`.
- `real[] curlSpecifier(guide g)` — Length-2 array `{initial_curl, final_curl}`.

### Pens (§6.3)
#### Color Spaces & Color Helpers
- `pen gray(real g)` — Grayscale pen; $g \in [0, 1]$ (0 = black, 1 = white).
- `pen rgb(real r, real g, real b)` — RGB pen; intensities $r, g, b \in [0, 1]$.
- `pen RGB(int r, int g, int b)` — RGB pen; integer values in $[0, 255]$.
- `pen cmyk(real c, real m, real y, real k)` — CMYK pen; $c, m, y, k \in [0, 1]$.
- `pen rgb(string s)` — Converts 6-character hex string `"RRGGBB"` to RGB pen.
- `string hex(pen p)` — Converts pen color to 6-character hex string.
- `pen invisible` — Draws nothing, but reserves space in bounding box (like TeX `\phantom`).
- `bool invisible(pen p)` — Returns `true` if `p` is invisible.
- `string colorspace(pen p)` — Returns `"gray"`, `"rgb"`, `"cmyk"`, or `""`.
- `real[] colors(pen p)` — Array of numeric color components.
- `pen gray(pen p)`, `pen rgb(pen p)`, `pen cmyk(pen p)` — Converts pen to specified colorspace.
- `pen colorless(pen p=currentpen)` — Strips color attributes from `p`, leaving linewidth/caps/linetype untouched.
- `pen hsv(real h, real s, real v)` — Hue $h \in [0, 360)$, saturation $s \in [0, 1]$, value $v \in [0, 1]$ (from `plain_pens.asy`).

#### Linetype & Width
- `pen linetype(real[] a, real offset=0, bool scale=true, bool adjust=true)` — Custom dash pattern. `a` contains alternating on/off lengths. `scale=true` interprets lengths in linewidth units; `adjust=true` stretches dashes to fit curve arclength.
- Predefined linetypes: `solid`, `dotted` (`{0,4}`), `dashed` (`{8,8}`), `longdashed` (`{24,8}`), `dashdotted` (`{8,8,0,8}`), `longdashdotted` (`{24,8,0,8}`).
- `pen Dotted(pen p=currentpen)` — Linewidth-proportional round dots: `linetype({0,3}) + 2*linewidth(p)`.
- `real[] linetype(pen p=currentpen)`, `real offset(pen p)`, `bool scale(pen p)`, `bool adjust(pen p)` — Linetype queries.
- `pen linewidth(real w)` — Sets stroke width in PostScript units (bp; 1 bp = 1/72 in; default 0.5 bp).
- `real linewidth(pen p=currentpen)` — Returns linewidth in bp.
- `void defaultpen(real w)` — Sets default line width to `w`.
- `pen operator +(pen p, real w)` / `pen operator +(real w, pen p)` — Equivalent to `p + linewidth(w)`.

#### Caps, Joins & Fillrules
- `pen linecap(int)` — Cap styles: `squarecap=linecap(0)`, `roundcap=linecap(1)` (default), `extendcap=linecap(2)`.
- `int linecap(pen p=currentpen)` — Query line cap integer.
- `pen linejoin(int)` — Join styles: `miterjoin=linejoin(0)`, `roundjoin=linejoin(1)` (default), `beveljoin=linejoin(2)`.
- `int linejoin(pen p=currentpen)` — Query line join integer.
- `pen miterlimit(real)` — PostScript miter limit (default 10.0).
- `real miterlimit(pen p=currentpen)` — Query miter limit.
- `pen fillrule(int)` — Insideness rules for `fill`, `clip`, `inside`: `zerowinding=fillrule(0)` (default), `evenodd=fillrule(1)`.
- `int fillrule(pen p=currentpen)` — Query fill rule integer.
- `pen basealign(int)` — Baseline alignment: `nobasealign=basealign(0)` (default, uses label bbox), `basealign=basealign(1)` (respects TeX baseline).
- `int basealign(pen p=currentpen)` — Query baseline alignment.

#### Fonts & Typography
- `pen fontsize(real size, real lineskip=1.2*size)` — Font size in TeX points (1 pt = 1/72.27 in; default 12pt). Nonstandard sizes need `import fontsize;`.
- `real fontsize(pen p=currentpen)`, `real lineskip(pen p=currentpen)` — Query font metrics.
- `pen font(string encoding, string family, string series, string shape)` — TeX NFSS font (default `font("OT1","cmr","m","n")`).
- `pen font(string name)` / `pen font(string name, real size)` — Fixed-size TeX font (e.g. `"cmr12"`, `"pcrr"`). `fontsize()` has no effect on fixed-size fonts.
- `pen fontcommand(string)` — Custom LaTeX font command string.
- Built-in PostScript fonts: `AvantGarde`, `Bookman`, `Courier`, `Helvetica`, `NewCenturySchoolBook`, `Palatino`, `TimesRoman`, `ZapfChancery`, `Symbol`, `ZapfDingbats` (each takes `string series="m", string shape="n"`).

#### Transparency, Patterns, Nib & Overwrite
- `pen opacity(real opacity=1, string blend="Compatible")` — Transparency in $[0, 1]$ (PDF output only). Blends: `"Normal"`, `"Multiply"`, `"Screen"`, `"Overlay"`, etc. Labels always have opacity 1.
- `pen pattern(string name)` — Tiling pattern registered via `patterns.add(name, pic)`.
- `pen makepen(path p)` — Defines custom polygonal pen nib from polygon `p` (need not be convex).
- `path nib(pen p)` — Extracts pen nib path (`nullpath` = default circular nib).
- `pen overwrite(int)` — Label collision modes: `Allow` (default), `Suppress`, `SuppressQuiet`, `Move`, `MoveQuiet`.
- `pen defaultpen()` — Returns current default pen.
- `void defaultpen(pen p)` — Sets default pen attributes.
- `void resetdefaultpen()` — Resets all pen attributes to system defaults.

#### Pen Algebra Semantics
- `pen + pen` — Non-associative binary operator:
  - Color components are ADDED: `red + green` produces yellow RGB `(1,1,0)`.
  - All other non-default attributes (linewidth, linetype, cap, join, font, fillrule) from the RIGHTMOST pen override the leftmost pen.
- `real * pen` — Scales color components by real number toward black (saturating at 1.0). Does NOT change linewidth.
- `transform * pen` — Deforms the pen nib polygon (e.g. `yscale(2)*currentpen` makes an elliptical nib). Does NOT scale linewidth.
- `pen + real` / `real + pen` — Evaluates to `pen + linewidth(real)`. Because rightmost non-default attribute overrides, this sets the linewidth to `real`, overriding earlier linewidths.

### Affine Transforms (§6.4)
6-component 2D affine transform $t = (t.x, t.y, t.xx, t.xy, t.yx, t.yy)$:
$$\begin{pmatrix} x' \\ y' \end{pmatrix} = \begin{pmatrix} t.x \\ t.y \end{pmatrix} + \begin{pmatrix} t.xx & t.xy \\ t.yx & t.yy \end{pmatrix} \begin{pmatrix} x \\ y \end{pmatrix}$$
Equivalent to PostScript matrix $[t.xx\quad t.yx\quad t.xy\quad t.yy\quad t.x\quad t.y]$.

- `transform identity` — Identity transform $(0,0,1,0,0,1)$.
- `transform shift(pair z)` / `transform shift(real x, real y)` — Translation by $z$ or $(x,y)$.
- `transform xscale(real x)` / `transform yscale(real y)` — Scaling along single axis.
- `transform scale(real s)` / `transform scale(real x, real y)` — Uniform or independent 2D scaling.
- `transform slant(real s)` — Slant/shear mapping $(x, y) \mapsto (x + sy, y)$.
- `transform rotate(real angle, pair z=(0,0))` — Counterclockwise rotation by `angle` degrees about point $z$.
- `transform reflect(pair a, pair b)` — Reflection across infinite line $a$--$b$.
- `transform zeroTransform` — All components 0.
- `transform inverse(transform t)` — Matrix inverse.
- `transform t^int n` — Integer power of transform.
- `bool isometry(transform t)` — Returns `true` iff $t$ preserves Euclidean distance.
- `transform shift(transform t)` — Translation part only: $(t.x, t.y, 0, 0, 0, 0)$.
- `transform shiftless(transform t)` — Linear part only: $(0, 0, t.xx, t.xy, t.yx, t.yy)$.
- Left-multiplication `t * obj` — Applicable to `pair`, `guide`, `path`, `pen`, `string`, `transform`, `frame`, `picture`, `Label`.
- Transform composition order: `t1 * t2 * obj` applies `t2` FIRST, then `t1`.

### Frames vs Pictures (§6.5)
- `frame` — PostScript coordinate canvas (absolute bp; fixed-size layout).
  - `bool empty(frame f)` — Returns `true` iff frame contains no drawing elements.
  - `void erase(frame f)` — Clears all objects from frame.
  - `pair min(frame f)`, `pair max(frame f)` — Lower-left and upper-right bounding box in PostScript coordinates.
  - `void add(frame dest, frame src)` — Appends contents of `src` to `dest`.
  - `void prepend(frame dest, frame src)` — Prepends contents of `src` beneath `dest`.
  - `frame align(frame f, pair align)` — Aligns frame along direction `align`.
  - `frame pack(pair align=2S, ... object inset[])` — Packs multiple objects into a single aligned frame.
- `picture` — User coordinate canvas (scaled to PostScript coordinates on shipout).
  - `currentpicture` — Default canvas.
  - `void size(picture pic=currentpicture, real x, real y=x, bool keepAspect=pic.keepAspect)` — Sizing constraints in PostScript bp.
  - `void unitsize(picture pic=currentpicture, real x, real y=x)` — Sets exact user coordinate unit length; overrides `size()`.
  - `void size(picture pic=currentpicture, real xsize, real ysize, pair min, pair max)` — Maps user box $(min, max)$ to rectangle $xsize \times ysize$.
  - `transform fixedscaling(picture pic=currentpicture, pair min, pair max, pen p=nullpen, bool warn=false)` — Fixed scale mapping user box to picture size.
  - `frame pic.fit(real xsize=pic.xsize, real ysize=pic.ysize, bool keepAspect=pic.keepAspect)` — Renders picture to frame.
  - `transform pic.calculateTransform()` — Returns affine transform mapping user coordinates to PostScript frame coordinates.
  - `frame pic.scale(real xsize=this.xsize, real ysize=this.ysize, bool keepAspect=this.keepAspect)` — Scales resulting frame, including labels and fixed-size markers.
  - `void shipout(string prefix=defaultfilename, picture pic=currentpicture, orientation orientation=orientation, ...)` — Writes picture to file. Implicitly called at exit.
  - `frame bbox(picture pic=currentpicture, real xmargin=0, real ymargin=xmargin, pen p=currentpen, filltype filltype=NoFill)` — Fits picture to frame surrounded by margin box.
  - `frame pad(picture pic=currentpicture, real xsize=pic.xsize, real ysize=pic.ysize, filltype filltype=NoFill)` — Pads picture to exact dimensions.
  - `pair min(picture pic, bool user=false)`, `pair max(picture pic, bool user=false)`, `pair size(picture pic, bool user=false)` — Bounds in user or PostScript coords.
  - `pair point(picture pic=currentpicture, pair dir, bool user=true)` — Bounding box boundary point ignoring fixed-size objects (labels, dots).
  - `pair truepoint(picture pic=currentpicture, pair dir, bool user=true)` — Bounding box boundary point including fixed-size objects.
  - `void add(picture dest=currentpicture, picture src, bool group=true, filltype filltype=NoFill, bool above=true)` — Merges `src` picture into `dest`.
  - `void add(picture dest=currentpicture, frame src, pair position=0, bool group=true, filltype filltype=NoFill, bool above=true)` — Places frame origin at user coordinate `position`.
  - `void add(picture dest=currentpicture, frame src, pair position, pair align, bool group=true, filltype filltype=NoFill, bool above=true)` — Aligns frame around `position`.
  - `void attach(picture dest=currentpicture, frame src, pair position=0, bool group=true, filltype filltype=NoFill, bool above=true)` — Adds frame and GROWS `dest` bounding box to accommodate fixed-size content.
  - `void attach(picture dest=currentpicture, frame src, pair position, pair align, bool group=true, filltype filltype=NoFill, bool above=true)` — Aligned attach.
  - `void erase(picture pic=currentpicture)` — Clears picture content without resetting size specifications.
  - `void save()`, `void restore()` — Saves and restores `currentpicture`, `currentpen`, `currentprojection`.
  - `void postscript(picture pic=currentpicture, string s[, pair min, pair max])` — Inserts raw PostScript.
  - `void tex(picture pic=currentpicture, string s[, pair min, pair max])` — Inserts raw TeX.
  - `void texpreamble(string s)`, `void texreset()`, `void usepackage(string s, string options="")` — TeX configuration.

### Deferred Drawing (§6.6)
- `typedef void drawer(frame f, transform t);` — Deferred drawing callback signature. `t` is user-to-PostScript affine transform computed at shipout.
- `void picture.add(drawer d)` — Registers deferred drawing callback on picture.
- `void picture.addPoint(pair user, pair truesize)` — Registers a user point offset by fixed PostScript coordinate `truesize` for autoscaling calculations.
- `void picture.addBox(pair userMin, pair userMax, pair trueMin=0, pair trueMax=0)` — Registers user-coordinate bounding box expanded by fixed PostScript margins.

## Options/parameters

- `size(picture pic=currentpicture, real x, real y=x, bool keepAspect=pic.keepAspect)`:
  - `x=0, y=0`: User coordinates are treated as PostScript points (bp); transform is `identity()`.
  - `x > 0, y=0` (or `x=0, y > 0`): Unconstrained in the zero axis; scales uniformly with the non-zero axis.
  - `keepAspect=true` (`Aspect`): Fits inside $x \times y$ rectangle, preserving aspect ratio.
  - `keepAspect=false` (`IgnoreAspect`): Stretches picture independently to fill exact $x \times y$ dimensions.
- `unitsize(picture pic=currentpicture, real x, real y=x)`: Sets user unit length in PostScript bp (e.g. `1cm`). When non-zero, overrides `size()`.
- `label(..., align=NoAlign, pen p=currentpen, filltype filltype=NoFill)`:
  - `align=NoAlign`: Centers label text bounding box at `position`.
  - Compass directions (`N`, `S`, `E`, `W`, `NE`, etc.): Displaces label by PostScript offset `align * labelmargin(p)`.
  - `Align`: Aligns bottom-left corner of label at `position`.
- `Label(..., embed=Rotate)`:
  - `Shift`: Translates only with picture transform.
  - `Rotate` (default): Translates and rotates with picture transform.
  - `Rotate(pair z)`: Rotates with picture-transformed vector $z$.
  - `Slant`: Translates, rotates, slants, and reflects.
  - `Scale`: Transforms fully with embedding picture including scaling.
- `linetype(real[] a, real offset=0, bool scale=true, bool adjust=true)`:
  - `scale=true`: Numbers in `a` are multiples of `linewidth(p)`. `scale=false`: numbers are absolute PostScript bp.
  - `adjust=true`: Automatically stretches dash pattern to fit total path arclength cleanly.
  - `offset`: Starting distance offset into dash pattern.
- `opacity(real opacity=1, string blend="Compatible")`:
  - `blend`: 17 standard PDF blending modes (`"Normal"`, `"Multiply"`, `"Screen"`, `"Overlay"`, `"Darken"`, `"Lighten"`, etc.). PDF output only; ignored in EPS.
- `intersect(path p, path q, real fuzz=-1)`:
  - `fuzz < 0`: Computes intersection to machine precision. `fuzz >= 0`: absolute error tolerance.
- `cut(path p, path knife, int n)`: Cuts `p` at $n$-th intersection (modulo total intersection count). Returns `slice` with `.before` and `.after`.
- `bbox(picture pic=currentpicture, real xmargin=0, real ymargin=xmargin, pen p=currentpen, filltype filltype=NoFill)`:
  - `filltype`: `FillDraw`, `Fill`, `NoFill`, `Draw`, `UnFill`, `RadialShade(penc, penr)`, `RadialShadeDraw`.
- `add(picture dest=currentpicture, frame src, pair position, pair align, bool group=true, filltype filltype=NoFill, bool above=true)`:
  - `align`: When omitted, frame ORIGIN is placed at `position` (unlike `label`, which centers).
  - `group=true`: GUI treats frame as a single atomic object.
  - `above=false`: Places added content underneath existing layers.

## Traps

1. **Postfix increment/decrement DO NOT EXIST**: Expressions `i++` and `i--` cause syntax errors because `--` is the path joiner. Use prefix `++i` and `--i`, or `(++i - 1)`.
2. **Integer division `/` returns `real`**: `3 / 2` evaluates to `1.5`, NOT `1`. For integer quotient division, use `#` (`3 # 2 == 1`) or `quotient(3, 2)`.
3. **Pair `*` is complex multiplication**: `(1, 2) * (3, 4)` computes $(1+2i)(3+4i) = (-5, 10)$, NOT $(3, 8)$. For componentwise multiplication, use `realmult(a, b)`.
4. **Virtual members `z.x`, `z.y`, `v.z` are read-only**: Assignment `z.x = 5;` is illegal. Reassign the full pair: `z = (5, z.y);`.
5. **Path vs Guide in loops ($O(n^2)$ vs $O(n)$)**: Appending to a `path` (`p = p -- pt;`) inside a loop copies the entire spline data structure on every iteration, leading to $O(n^2)$ quadratic slowdown. Accumulate into an unresolved `guide` (`g = g -- pt;`, $O(n)$ linear time), and assign to `path p = g;` once.
6. **Guides resolve only at draw/cast time**: Incremental resolution of a `path` freezes spline knots before future points are known, introducing sharp kinks. A `guide` keeps knots unresolved so joining curves preserves smooth $C^2$ continuity.
7. **Direction specifiers on `nullpath` flip nodes**: The guide `a .. {up}nullpath .. b` attaches `{up}` to node `b`, NOT node `a`.
8. **`length(p)` vs `size(p)`**: `length(path)` is the number of spline segments. `size(path)` is the number of nodes. For non-cyclic paths, `size(p) == length(p) + 1`; they are equal ONLY if `p` is cyclic.
9. **`intersect()` returns `real[0]` (empty array) when no intersection exists**: Indexing `intersect(p, q)[0]` directly without checking `.length > 0` causes a runtime out-of-bounds error.
10. **`times(path, real)` vs `times(path, pair)` axis trap**: `times(p, x)` finds intersections with the VERTICAL line $X = x$. `times(p, z)` finds intersections with the HORIZONTAL line $Y = z.y$.
11. **`angle((0,0))` and `degrees((0,0))` throw runtime errors**: By default, passing `(0,0)` crashes. Pass `warn=false` (`angle((0,0), false)`) to return 0 safely.
12. **`extension(P, Q, p, q)` on parallel lines**: Returns `(infinity, infinity)` instead of throwing an exception.
13. **Pen `+` mixes colors additively**: `red + green` produces yellow, NOT green. Adding a pen does not overwrite earlier colors unless stripped with `colorless(p)`.
14. **Pen `+` real overrides line width**: `pen + w` evaluates to `pen + linewidth(w)`. Because the rightmost non-default attribute overrides, `p + 2.0` does NOT increase the existing linewidth by 2.0; it sets the linewidth to 2.0 bp.
15. **`real * pen` vs `transform * pen`**: `0.5 * red` scales color saturation toward black; it does NOT change linewidth. `yscale(2) * pen` deforms the pen nib polygon; it does NOT scale the stroke width. Linewidth is changed ONLY via `linewidth()`.
16. **Pen `+` is non-associative**: Operator grouping matters when combining pens with multiple conflicting attributes.
17. **`invisible` pen expands bounding box**: Like TeX `\phantom`, objects drawn with `invisible` allocate space in canvas autoscaling.
18. **`fillrule` affects only `clip`, `fill`, `inside`**: Has zero effect on `draw()` strokes.
19. **`opacity()` is PDF-only**: Silently ignored when rendering to EPS/PostScript formats. Text labels always have opacity 1 regardless of pen opacity.
20. **Transform application order**: In `T1 * T2 * obj`, `T2` applies FIRST. E.g., `rotate(45) * xscale(2)` scales $x$ first, then rotates 45 degrees.
21. **User-to-PostScript scale is unknown until shipout**: Autoscaling factors are calculated only at `shipout()`. Any routine needing fixed-size ink (arrows, dots, flowchart blocks) must use deferred drawer `void(frame f, transform t)` and register bounds via `addPoint()` or `addBox()`.
22. **`add(frame, position)` vs `label()` alignment**: `label(s, pos)` centers the string at `pos` by default. `add(pic, frame, pos)` aligns the frame's ORIGIN `(0,0)` at `pos`.
23. **`attach()` vs `add()`**: `attach` automatically expands the destination picture bounding box to enclose the newly added fixed-size frame; plain `add` does not expand bounds for fixed-size frames placed outside user coordinates.
24. **`overwrite=Move` can enlarge output figure**: Collision shifting happens in PostScript coordinates at shipout time and can push the final dimensions beyond the requested `size()`.
25. **String escape rules**: Double-quoted strings `"..."` only process `\"` and `\\` (allowing raw LaTeX commands like `\frac`). Single-quoted strings `'...'` process standard C escape sequences (`\n`, `\t`, `\x41`, octals). Indexing `s[pos]` out of bounds returns `""` rather than erroring.
26. **Deferred drawing callback for flowchart connectors**: Flowchart blocks store their centers in user coordinates but their dimensions and ports in PostScript coordinates. Connecting them requires the user-to-PostScript transform `t`, which is only available inside a deferred callback: `pic.add(new void(picture p, transform t) { blockconnector operator -- = blockconnector(p, t); b1 -- Right -- Arrow -- b2; });`.

## Compile-gated examples

### Example 1: Complex Pair Algebra, Path Construction & Geometric Queries
```asy
// Verifies pair arithmetic, integer division, cubic path queries & intersections
int q = 7 # 2;                // integer division: 3
real r = 7 / 2;               // real division: 3.5
pair z = (3, 4);
pair w = 1 + 2*I;             // I == (0, 1)
pair prod = z * w;            // complex multiplication: (3+4i)*(1+2i) = (-5, 10)
pair elem = realmult(z, w);   // elementwise product: (3, 8)

path p = (0,0){up}::(2,2){right}::(4,0){down};
path knife = (0,1)--(4,1);

real[] t = intersect(p, knife);
assert(t.length == 2);
pair ipt = point(p, t[0]);
path segment = subpath(p, 0, t[0]);
real len = arclength(segment);
pair tangent = dir(p, t[0]);

// Counterclockwise box encircling point (2, 1)
path ccw_box = (0,0)--(4,0)--(4,2)--(0,2)--cycle;
int wn = windingnumber(ccw_box, (2, 1));

assert(q == 3 && r == 3.5);
assert(prod == (-5, 10) && elem == (3, 8));
assert(wn == 1);
```

### Example 2: Incremental Guide Loop, Pen Algebra & Transform Ordering
```asy
// Verifies linear-time guide accumulation, pen attribute combination, transform order
guide g;
for (int i = 0; i <= 5; ++i) {
  g = g .. (i, sin(i));       // O(n) linear accumulation
}
path solved = g;              // resolves to smooth cubic spline once

pen base_pen = red + dashed;
pen yellow_mix = base_pen + green;        // color mixing: red + green = yellow
pen blue_pure = colorless(base_pen) + blue;// colorless() strips red

pen custom = linewidth(1.5) + linecap(0) + linejoin(1);
pen scaled_color = 0.5 * blue;            // scales color towards black
pen override_width = custom + 2.0;        // sets linewidth to 2.0 (overrides 1.5)

// Transform composition: xscale(2) applies FIRST, then rotate(90)
transform T = rotate(90) * xscale(2);
pair transformed = T * (1, 0);            // (1,0) -> (2,0) -> (0,2)

assert(abs(transformed.x) < 1e-10 && abs(transformed.y - 2) < 1e-10);
assert(linewidth(override_width) == 2.0);
assert(length(solved) == 5);
```

### Example 3: Frames, Canvas Sizing, Attach & Deferred Drawing Callbacks
```asy
// Verifies frame construction, picture unitsize, attach, and deferred drawer
picture pic;
unitsize(pic, 1cm);                       // 1 user unit = 1cm; overrides size()

// Draw user-coordinate geometry
draw(pic, (0,0)--(2,2), blue);

// Frame construction in PostScript coordinates (bp)
frame f;
fill(f, circle((0,0), 4), red);

// attach expands picture bounding box to accommodate fixed-size frame
attach(pic, f, (2,2), NE);

// Deferred drawing: drawer signature void(frame f, transform t)
// t is the user-to-PostScript affine transform computed at shipout time
pic.add(new void(frame f, transform t) {
  fill(f, circle(t * (1, 1), 3), green);  // fixed 3bp circle at user (1,1)
});

// Register bounding box extents of the deferred element
pair margin = (3, 3) + max(currentpen);
pic.addPoint((1, 1), -margin);
pic.addPoint((1, 1), margin);

frame rendered = pic.fit();
assert(!empty(rendered));
```
