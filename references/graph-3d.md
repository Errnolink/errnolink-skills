# 3D Graphics, Surfaces & Coordinate Systems (§8.2–8.3, §8.11, §8.19, §8.21, pp. 146–164, 177–178, 184–185)
> Read this when drawing: 3D curves and paths (`guide3`/`path3`), Bezier surfaces and patches, 3D coordinate frames, projections (`orthographic`/`perspective`/`oblique`), lights and PBR materials, parametric/rotation/matrix surfaces, solids (`cylinder`/`cone`/`sphere`/`revolution`), tubes and swept paths, 3D axes (`xaxis3`/`yaxis3`/`zaxis3`/`axes3`) and ticks (`InTicks`/`OutTicks`/`InOutTicks`), 3D labels (`Billboard` vs `Embedded`), binary space partition (`bsp`), 3D grids (`grid3`), and 3D rendering modes (`render`, WebGL HTML, PRC PDF, vector `-render 0`).

## Syntax recap
```asy
import graph3;
size(200, 150, IgnoreAspect);
currentprojection = perspective(10, 8, 4);

real f(pair z) { return 0.5 + exp(-abs(z)^2); }
surface s = surface(f, (-1, -1), (1, 1), nx=10, Spline);

draw(s, lightgray, meshpen=black+thick(), nolight, render(merge=true));
xaxis3(Label(XZ()*"$x$", align=-3Y), Bounds, red, InTicks(Label, 2, 2));
yaxis3(Label(YZ()*"$y$", align=-3X), Bounds, red, InTicks(beginlabel=false, Label, 2, 2));
zaxis3(XYZero(extend=true), red, Arrow3);
label("$O$", O, -Z+Y, red);
```
- `import graph3;` loads 3D geometry (`three`), surface constructors, 3D axis systems, and projection parameters.
- `currentprojection = perspective(10, 8, 4);` sets global camera viewpoint (assigned globally, NOT passed to `draw`).
- `surface s = surface(f, (-1,-1), (1,1), nx=10, Spline);` constructs bicubic Bezier surface over rectangular domain.
- `draw(s, lightgray, meshpen=black+thick(), nolight, render(merge=true));` renders surface with mesh lines and flat shading.
- `xaxis3(Label(XZ()*"$x$", align=-3Y), Bounds, red, InTicks(...));` draws bounding box axis with tick marks and plane-aligned label.
- `zaxis3(XYZero(extend=true), red, Arrow3);` draws centered z axis through origin with 3D arrowhead.

## Vocabulary

### 3D Core Types & Constants (§8.2)
- `triple` — 3D coordinate point/vector `(x, y, z)`; replaces 2D `pair`.
- `guide3` — unresolved 3D spline trajectory before solving control points.
- `path3` — resolved 3D cubic spline composed of Bezier segments.
- `surface` — 3D surface composed of an array of Bezier patches or triangles.
- `triple O=(0,0,0), X=(1,0,0), Y=(0,1,0), Z=(0,0,1)` — standard 3D origin and basis vectors.
- `path3 path3(path p, triple plane(pair)=XYplane)` — lifts 2D path into 3D plane (default XY plane).
- `path path(path3 p, pair P(triple)=xypart)` — projects 3D path into 2D plane.
- `void size3(picture pic=currentpicture, real x, real y=x, real z=y, bool keepAspect=pic.keepAspect)` — sets maximal 3D bounding dimensions before deferred projection.
- `frame pic.fit3(projection P=currentprojection)` — fits 3D picture to 3D frame under given projection.
- `void add(picture dest=currentpicture, frame src, triple position=(0,0,0))` — adds 3D frame into picture at triple position.

### Guide3 Connectors & Curves (§8.2)
- `--` — straight line segment connecting two 3D nodes.
- `..` — cubic spline connector using 3D rotation-invariant generalization of Hobby's algorithm.
- `::` — inflection-free cubic spline abbreviation for `..tension atleast 1..`.
- `---` — straight line connector with continuous curvature transition (`..tension atleast infinity..`).
- `&` — path concatenation; joins two paths stripping duplicate juncture node.
- `{triple dir}` — explicit tangent direction specifier at a node (e.g. `{Z}`, `{Y}`, `{(1,1,0)}`).
- `{curl c}` — sets endpoint boundary condition curvature parameter $c$.
- `tension atleast t` / `tension t1 and t2` — sets curve tension between nodes.
- `path3 circle(triple c, real r, triple normal=Z)` — approximate circle perpendicular to `normal`.
- `path3 arc(triple c, real r, real theta1, real phi1, real theta2, real phi2, triple normal=O)` — spherical circular arc.
- `path3 arc(triple c, triple v1, triple v2, triple normal=O, bool direction=CCW)` — circular arc between two points.
- `path3 Circle(triple c, real r, triple normal=Z, int n=nCircle)` — high-accuracy 3D circle (`graph3`).
- `path3 Arc(triple c, real r, real theta1, real phi1, real theta2, real phi2, triple normal=O, int n=nCircle)` — high-accuracy 3D arc (`graph3`).
- `path3 plane(triple u, triple v, triple O=O)` — parallelogram `O--O+u--O+u+v--O+v--cycle` with normal `cross(u, v)`.
- `path3[] box(triple v1, triple v2)` — wireframe box bounding opposite corners `v1` and `v2`.

### Predefined 3D Geometry Primitives (§8.2)
- `path3 unitcircle3` — unit circle in the XY plane `X..Y..-X..-Y..cycle`.
- `path3 unitsquare3` — unit square path in XY plane `O--X--X+Y--Y--cycle`.
- `path3[] unitbox` — unit box wireframe `box(O, (1,1,1))`.
- `surface unitdisk` — circular planar disk of radius 1 in XY plane.
- `surface unitplane` — square planar surface $[0,1]\times[0,1]$ in XY plane.
- `surface unitcube` — 6-sided solid cube with unit edge length $[0,1]^3$.
- `surface unitcylinder` — open cylinder of radius 1 along Z from $z=0$ to $z=1$.
- `surface unitcone` — open cone of base radius 1 with apex at $(0,0,1)$.
- `surface unitsolidcone` — closed cone including base disk.
- `surface unitfrustum(real t1, real t2)` — frustum slice of unit cone between heights $t1$ and $t2$.
- `surface unitsphere` — unit sphere centered at origin with radius 1.
- `surface unithemisphere` — upper unit hemisphere ($z \ge 0$).

### Projections & Standard Views (§8.2)
- `projection currentprojection` — global projection variable (initially `perspective(5, 4, 2)`).
- `projection orthographic(triple camera, triple up=Z, triple target=O, real zoom=1, pair viewportshift=0, bool showtarget=true, bool center=true)` — parallel projection along `unit(camera)`.
- `projection orthographic(real x, real y, real z, triple up=Z, triple target=O, real zoom=1, pair viewportshift=0, bool showtarget=true, bool center=true)` — 3-coordinate orthographic overload.
- `projection perspective(triple camera, triple up=Z, triple target=O, real zoom=1, real angle=0, pair viewportshift=0, bool showtarget=true, bool autoadjust=true, bool center=autoadjust)` — perspective projection looking at `target`.
- `projection perspective(real x, real y, real z, triple up=Z, triple target=O, ...)` — 3-coordinate perspective overload.
- `projection oblique(real angle)` / `oblique` / `obliqueZ` — oblique projection $(x,y,z) \to (x - 0.5z, y - 0.5z)$ (optional angle for negative z-axis).
- `projection obliqueX(real angle)` / `obliqueY(real angle)` — oblique projections along X and Y axes.
- `triple camera(real alpha, real beta)` — camera position with x-axis at angle $\alpha$ and y-axis at angle $\beta$ below horizontal.
- `projection LeftView, RightView, FrontView, BackView, BottomView, TopView` — standard orthographic engineering views (`orthographic(-X, showtarget=true)`, etc.).
- `projection[][] ThreeViewsUS, SixViewsUS, ThreeViewsFR, SixViewsFR, ThreeViews, SixViews` — standard multi-view projection grids.
- `void addViews(picture dest=currentpicture, picture src, projection[][] views=SixViewsUS, bool group=true, filltype filltype=NoFill)` — adds multi-view projection array to destination picture.

### Project & Invert Operations (§8.2)
- `pair project(triple v, projection P=currentprojection)` — projects 3D point to 2D canvas pair.
- `path project(path3 p, projection P=currentprojection)` — projects 3D path to 2D path.
- `triple invert(pair z, triple normal, triple point, projection P=currentprojection)` — unprojects 2D point onto 3D plane passing through `point` with `normal`.
- `triple invert(pair z, projection P=currentprojection)` — unprojects 2D point onto 3D projection plane.
- `triple invert(pair dir, triple v, projection P=currentprojection)` — unprojects 2D direction to 3D direction relative to 3D point `v`.

### 3D Transforms (§8.2)
- `transform3 shift(triple v)` — translates by 3D vector `v`.
- `transform3 scale3(real s)` — uniform 3D scaling by factor `s`.
- `transform3 scale(real x, real y, real z)` — non-uniform scaling along X, Y, Z.
- `transform3 xscale3(real x), yscale3(real y), zscale3(real z)` — single-axis scaling.
- `transform3 rotate(real angle, triple v)` — rotates by `angle` (degrees) about axis line `O--v`.
- `transform3 rotate(real angle, triple u, triple v)` — rotates by `angle` (degrees) about axis line `u--v`.
- `transform3 reflect(triple u, triple v, triple w)` — reflects across plane passing through `u, v, w`.
- `transform3 identity4` — 3D identity transform (note: NOT `identity`).

### Lights, Materials & PBR (§8.2)
- `struct material` — PBR surface material: `pen[] p` (diffuse, emissive, specular pens), `real opacity`, `real shininess` (0..1, scaled $\times 128$), `real metallic`, `real fresnel0`, `bool lightOn`.
- `material material(pen diffusepen=black, pen emissivepen=black, pen specularpen=mediumgray, real opacity=opacity(diffusepen), real shininess=defaultshininess, real metallic=defaultmetallic, real fresnel0=defaultfresnel0, bool lightOn=true)` — constructs PBR material; `pen` and `pen[]` implicitly cast to `material` / `material[]`.
- `material emissive(material m, bool colors=false)` — strips specular/diffuse lighting, treating surface as self-luminous emissive color.
- `struct light` — lighting model: `real[][] diffuse`, `real[][] specular`, `pen background=nullpen`, `real specularfactor`, `triple[] position` (directional light vectors).
- `light Headlamp` — default lighting attached to viewer camera (`light(white, specular=gray(0.7), specularfactor=3, dir(42,48))`).
- `light White` — three-point studio lighting setup.
- `light Viewport` — single directional headlight from `(0.25, -0.25, 1)`.
- `light nolight` — unlit flat shading (disables diffuse/specular shading).
- `currentlight.background = black + opacity(0.0)` — makes WebGL/3D canvas background completely transparent.

### Surface Drawing & Constructors (§8.2, §8.3)
- `void draw(picture pic=currentpicture, surface s, int nu=1, int nv=1, material surfacepen=currentpen, pen meshpen=nullpen, light light=currentlight, light meshlight=nolight, string name="", render render=defaultrender)` — renders 3D surface with optional mesh subdivision lines `nu, nv`.
- `void draw(picture pic=currentpicture, surface s, int nu=1, int nv=1, material[] surfacepen, pen[] meshpen=nullpens, ...)` — draws surface with per-patch materials and mesh pens.
- `void draw(picture pic=currentpicture, triple[] v, int[][] vi, triple[] n={}, int[][] ni=vi, material m=currentpen, pen[] p={}, int[][] pi=vi, light light=currentlight)` — indexed triangle tessellation.
- `surface surface(path3 external, triple[] internal=new triple[], pen[] colors=new pen[], bool3 planar=default)` — builds patch (4 nodes), Bezier triangle (3 nodes), or bezulate planar surface from cyclic `path3`.
- `surface surface(path p, triple plane(pair)=XYplane)` — constructs planar 3D surface from 2D path mapped to 3D plane.
- `surface surface(real f(pair z), pair a, pair b, int nx=nmesh, int ny=nx, splinetype xsplinetype=null, splinetype ysplinetype=xsplinetype, bool cond(pair z)=null)` — explicit height surface $z = f(x, y)$ over rectangular box $a \le (x,y) \le b$.
- `surface surface(real[][] f, pair a, pair b, splinetype xsplinetype=null, splinetype ysplinetype=xsplinetype, bool[][] cond={})` — surface from 2D height matrix on grid over box `(a, b)`.
- `surface surface(real[][] f, real[] x, real[] y, ...)` — surface from 2D height matrix over explicit non-uniform coordinates $x, y$.
- `surface surface(triple[][] f, bool[][] cond={})` — surface interpolating explicit 2D grid of 3D points.
- `surface surface(triple f(pair z), pair a, pair b, int nu=nmesh, int nv=nu, splinetype[] usplinetype=null, splinetype[] vsplinetype=Spline, bool cond(pair z)=null)` — parametric surface $(x,y,z) = f(u,v)$ over $[a.x, b.x] \times [a.y, b.y]$.
- `surface surface(triple f(pair z), real[] u, real[] v, ...)` — parametric surface over discrete parameter arrays $u, v$.
- `surface surface(triple c, path3 g, triple axis, int n=nslice, real angle1=0, real angle2=360, pen color(int i, real j)=null)` — rotational surface revolving path `g` about axis line `c--c+axis`.
- `surface extrude(path p, triple axis=Z)` / `surface extrude(Label L, triple axis=Z)` — extrudes 2D path or Label along 3D axis vector.

### Solids of Revolution (§8.19)
- `import solids;` — loads surface-of-revolution geometry and wireframe skeleton generation.
- `revolution cylinder(triple c=O, real r, real h, triple axis=Z)` — circular cylinder of radius $r$, height $h$, centered at $c$ aligned with `axis`.
- `revolution cone(triple c=O, real r, real h, triple axis=Z, int n=nslice)` — circular cone of radius $r$, height $h$, with apex at $c + h\cdot\mathrm{unit}(\mathrm{axis})$.
- `revolution sphere(triple c=O, real r, int n=nslice)` — sphere of radius $r$ centered at $c$.
- `revolution revolution(triple c=O, path3 g, triple axis=Z, real angle1=0, real angle2=360)` — general surface of revolution around axis `c--c+axis`.
- `surface surface(revolution r, int n=nslice, pen color(int i, real j)=null)` — converts `revolution` struct to renderable `surface`.
- `void draw(picture pic=currentpicture, revolution r, int m=0, int n=nslice, pen frontpen=currentpen, pen backpen=frontpen, pen longitudinalpen=frontpen, pen longitudinalbackpen=backpen, light light=currentlight, string name="", render render=defaultrender, projection P=currentprojection)` — draws 3D wireframe skeleton with separated front and back hidden-line pens.
- `struct skeleton { struct curve { path3[] front; path3[] back; } curve transverse; curve longitudinal; }` — stores separated front/back silhouette curves.

### Tubes & Swept Surfaces (§8.2, §8.21)
- `tube tube(path3 p, real width, render render=defaultrender)` — constructs thick 3D line as tube surface around spine path `p`; returns `tube { surface s; path3 center; }` (`three`).
- `import tube;` — loads generalized swept tube module (§8.21).
- `surface tube(path3 g, coloredpath section, transform T(real)=identity(), real corner=1, real relstep=0)` — sweeps 2D cross-section `section` along 3D spine `g` with localized transform `T(t)`.
- `coloredpath coloredpath(path p, pen[] pens(real), int colortype=coloredSegments)` — cross-section path with time-dependent coloring.
- `coloredpath coloredpath(path p, pen[] pens=new pen[] {currentpen}, int colortype=coloredSegments)` — uniform cross-section path (auto-cast from plain `path`).

### 3D Axes & Coordinate Systems (§8.3)
- `void xaxis3(picture pic=currentpicture, Label L="", axis axis=YZZero, real xmin=-infinity, real xmax=infinity, pen p=currentpen, ticks3 ticks=NoTicks3, arrowbar3 arrow=None, margin3 margin=NoMargin3, bool above=false, projection P=currentprojection)` — draws 3D x-axis.
- `void yaxis3(picture pic=currentpicture, Label L="", axis axis=XZZero, real ymin=-infinity, real ymax=infinity, pen p=currentpen, ticks3 ticks=NoTicks3, arrowbar3 arrow=None, margin3 margin=NoMargin3, bool above=false, projection P=currentprojection)` — draws 3D y-axis.
- `void zaxis3(picture pic=currentpicture, Label L="", axis axis=XYZero, real zmin=-infinity, real zmax=infinity, pen p=currentpen, ticks3 ticks=NoTicks3, arrowbar3 arrow=None, margin3 margin=NoMargin3, bool above=false, projection P=currentprojection)` — draws 3D z-axis.
- `void axes3(picture pic=currentpicture, Label xlabel="", Label ylabel="", Label zlabel="", bool extend=false, triple min=(-infinity,-infinity,-infinity), triple max=(infinity,infinity,infinity), pen p=currentpen, pen py=p, pen pz=p, arrowbar3 arrow=None, margin3 margin=NoMargin3, projection P=currentprojection)` — draws all 3 axes at once.
- `axis YZZero(triple align=O, bool extend=false), XZZero(...), XYZero(...)` — axis passing through origin in respective coordinate plane.
- `axis YZEquals(real y, real z, triple align=O, bool extend=false), XZEquals(real x, real z, ...), XYEquals(real x, real y, ...)` — axis fixed at specific plane coordinates.
- `axis Bounds(int type=Both, int type2=Both, triple align=O, bool extend=false)` — bounding box axis edges; `type` and `type2` in `{Min, Max, Both}` select which of the 4 bounding box edges are drawn.
- `void axis(picture pic=currentpicture, Label L="", path3 g, path3 g2=nullpath3, pen p=currentpen, ticks3 ticks, ticklocate locate, arrowbar3 arrow=None, margin3 margin=NoMargin3, int[] divisor=new int[], bool above=false, bool opposite=false)` — general 3D axis along arbitrary path `g`.
- `void xtick3(picture pic=currentpicture, Label L, real x, triple dir=Y, string format="", real size=Ticksize, pen p=currentpen)` — manual single tick on x-axis.
- `void labelx3(picture pic=currentpicture, Label L="", real x, align align=-Y, string format="", pen p=currentpen)` — manual single coordinate label on x-axis.
- `scale(Linear, Linear, Log)` — sets independent forward/inverse scales for X, Y, and Z axes.
- `scale(bool x, bool y, bool z)` / `scale(true)` — enables automatic axis limit calculation.

### 3D Ticks & Locations (§8.3)
- `ticks3 NoTicks3` — disables tick generation on 3D axis.
- `ticks3 InTicks(Label format="", ticklabel ticklabel=null, bool beginlabel=true, bool endlabel=true, int N=0, int n=0, real Step=0, real step=0, bool begin=true, bool end=true, tickmodifier modify=None, real Size=0, real size=0, bool extend=false, pen pTick=nullpen, pen ptick=nullpen)` — inward ticks relative to bounding box.
- `ticks3 OutTicks(...)` — outward ticks pointing away from bounding box (standard for scientific plots).
- `ticks3 InOutTicks(...)` — bidirectional ticks crossing the axis line.
- `ticks3 InTicks/OutTicks(Label format="", ..., real[] Ticks, real[] ticks=new real[], ...)` — explicit major/minor tick coordinate arrays.

### 3D Labels & Plane Transforms (§8.2)
- `void label(picture pic=currentpicture, Label L, triple position, align align=NoAlign, pen p=currentpen, light light=nolight, string name="", render render=defaultrender, interaction interaction=settings.autobillboard ? Billboard : Embedded)` — places 3D label.
- `interaction Billboard` — label automatically and interactively rotates to face camera.
- `interaction Embedded` — label is fixed in 3D scene space and rotates as a 3D surface patch.
- `transform3 XY, YZ, ZX, YX, ZY, XZ` — predefined transforms laying labels flat into coordinate planes: `Label(XY()*"$x$", align=-3Y)`.
- `transform3 XY(projection P=currentprojection), YZ(P), ZX(P), ...` — viewpoint-adapted plane transforms rotating/flipping label for readability.
- `transform3 planeproject(triple n, triple O=O, triple dir=n)` — projection transform onto plane with normal $n$ through $O$.
- `transform3 planeproject(path3 p, triple dir=normal(p))` — projection transform onto plane defined by planar path $p$.
- `triple normal(path3 p)` — returns unit normal vector to planar 3D path.

### 3D Arrows, Bars & Annotations (§8.2)
- `arrowbar3 Arrow3(arrowhead3 arrowhead=DefaultHead3, real size=0, real angle=arrowangle, filltype filltype=null, position position=EndPoint, material arrowheadpen=nullpen)` (synonym `EndArrow3`) — 3D arrowhead at path end.
- `arrowbar3 BeginArrow3, MidArrow3, Arrows3` — arrowheads at start, midpoint, or both ends.
- `arrowbar3 ArcArrow3, BeginArcArrow3, MidArcArrow3, ArcArrows3` — curved arrowheads along 3D arcs.
- `arrowhead3 DefaultHead3, HookHead3, TeXHead3` — true 3D conical/surfaced arrowheads.
- `arrowhead3 DefaultHead2(triple normal=O), HookHead2, TeXHead2` — 2D planar arrowheads lifted into 3D.
- `arrowbar3 Bar3(real size=0, triple dir=O)` (synonym `EndBar3`), `BeginBar3, Bars3` — perpendicular end bars (`dir=O` aligns perpendicular to path and viewpoint).
- `margin3 NoMargin3, BeginMargin3, EndMargin3, Margin3, Margins3, PenMargin3, DotMargin3` — 3D path end-clipping margins.
- `void pixel(picture pic=currentpicture, triple v, pen p=currentpen, real width=1)` — draws single pixel at triple coordinate.

### BSP Hidden Surface Removal (§8.2)
- `import bsp;` — binary space partitioning for exact hidden-surface removal of planar polygon scenes.
- `struct face` — face structure derived from `picture`, represents a planar polygonal face.
- `face faces.push(path3 p)` — pushes planar path `p` into face array and returns sub-picture handle.
- `void add(picture pic=currentpicture, face[] faces, projection P=currentprojection)` — splits, clips, and sorts faces back-to-front, drawing them into `pic`.
- `transform transform(triple u, triple v, triple O=O, projection P=currentprojection)` — computes 2D projection transform for labels on plane $O + u \times v$.

### 3D Grid Systems (§8.11)
- `import grid3;` — Philippe Ivaldi's 3D coordinate plane grid module.
- `void grid3(picture pic=currentpicture, grid3routinetype gridroutine=XYZgrid, int N=0, int n=0, real Step=0, real step=0, bool begin=true, bool end=true, pen pGrid=grey, pen pgrid=lightgrey, bool above=false)` — draws 3D grid lines across bounding box.
- `grid3routinetype XYZgrid(position pos=Relative(0))` — 3D grids on all three coordinate planes (XY, YZ, ZX).
- `grid3routine XYgrid(position pos=Relative(0)), XZgrid(...), YZgrid(...)` — single-plane grid routines.
- `grid3routines XYXgrid, ZXZgrid, ZYZgrid, XY_XZgrid, YX_YZgrid, ZX_ZYgrid` — paired/multi-plane grid routines.

### Rendering Modes & Export Formats (§8.2)
- `settings.outformat=""` + `-V` + `settings.render=-1` — native interactive OpenGL rendering window.
- `settings.outformat="html"` (or `asy -f html`) — generates standalone interactive WebGL/HTML file.
- `settings.offline=true` — embeds ~48 kB AsyGL library into WebGL HTML for offline viewing without CDN.
- `settings.absolute=true` — preserves designed canvas pixel size in WebGL instead of dynamic browser resize.
- `settings.render=n` — rasterizes 3D scene to bitmap format at $n$ pixels per bp (default $n=2$, internally $2\times$ supersampled).
- `settings.antialias=1` — disables $2\times$ internal supersampling for rasterized 3D rendering.
- `settings.outformat="pdf"; settings.prc=true;` — embeds interactive 3D PRC model inside PDF (MUST precede `import three;`); viewable in Adobe Reader 9.0+ using LaTeX `media9`.
- `settings.outformat="v3d"` / `settings.v3d=true` — portable compressed vector format or PDF-embedded V3D.
- `settings.render=0` (or `asy -render 0`) — pure 2D vector projection (PostScript/PDF); no hardware 3D; surfaces rendered as vector patches; ideal with `bsp` or piecewise planar meshes.

## Options/parameters

### Projection Parameters (`orthographic` & `perspective`)
| Parameter | Default | Effect |
|---|---|---|
| `triple camera` / `(x, y, z)` | *(required)* | Camera position looking toward `target`; for orthographic, defines parallel view ray `unit(camera)`. |
| `triple up` | `Z` | Orientation vector that points toward the top of the viewport. |
| `triple target` | `O` | Focal center point in 3D scene space. |
| `real zoom` | `1` | View magnification factor. |
| `real angle` | `0` | (perspective only) Field-of-view angle in degrees (0 = auto-computed). |
| `pair viewportshift` | `0` | 2D offset applied to final rendered viewport. |
| `bool showtarget` | `true` | Expands bounding box to include `target`. |
| `bool autoadjust` | `true` | (perspective only) Automatically pushes camera outside bounding box under rotation. |
| `bool center` | `true` (ortho) / `autoadjust` (persp) | Adjusts `target` to center of scene bounding volume. |

### Surface Drawing Parameters (`draw(surface)`)
| Parameter | Default | Effect |
|---|---|---|
| `int nu, nv` | `1, 1` | Subdivisions for drawing optional mesh lines across each Bezier patch (ignored for Bezier triangles). |
| `material surfacepen` | `currentpen` | Material/color used for surface interior filling; `nullpen` disables filling (mesh-only). |
| `pen meshpen` | `nullpen` | Pen used to stroke mesh grid lines across patches; e.g. `black+thick()`. |
| `light light` | `currentlight` | Lighting model applied to surface; pass `nolight` for unshaded flat colors. |
| `light meshlight` | `nolight` | Lighting model applied to mesh lines. |
| `string name` | `""` | Part-name prefix in PRC/WebGL object tree. |
| `render render` | `defaultrender` | Rendering struct flags: `render(bool merge=true, bool tessellate=false)`. |

### Material & Light Parameters
| Parameter | Type / Default | Effect |
|---|---|---|
| `diffusepen` | `black` | Base surface diffuse reflection color. |
| `emissivepen` | `black` | Self-luminous surface emission color (unaffected by lights). |
| `specularpen` | `mediumgray` | Specular highlight color reflected toward camera. |
| `opacity` | `opacity(diffusepen)` | Transparency alpha (0.0 = fully transparent, 1.0 = fully opaque). |
| `shininess` | `defaultshininess` (0.5) | Specular exponent factor ($0..1$, multiplied by 128 for Phong model). |
| `metallic` | `defaultmetallic` (0.0) | Metallic reflection coefficient. |
| `fresnel0` | `defaultfresnel0` (0.04) | Reflectance rate at normal incidence. |
| `lightOn` | `true` | Enables/disables diffuse and specular lighting calculation. |
| `currentlight.background` | `nullpen` | Canvas background color; `black+opacity(0.0)` for WebGL transparency. |

### 3D Axis Parameters (`xaxis3`, `yaxis3`, `zaxis3`, `axes3`)
| Parameter | Default | Effect |
|---|---|---|
| `Label L` | `""` | Axis label string/Label; flat orientation via `Label(XY()*"$x$", align=-3Y)`. |
| `axis axis` | `YZZero` / `XZZero` / `XYZero` | Placement type: plane zero, `Equals(coord)`, or `Bounds(Min/Max/Both)`. |
| `real xmin, xmax` | `-infinity, infinity` | Axis coordinate span; infinite values autoscale to picture bounding box. |
| `ticks3 ticks` | `NoTicks3` | 3D tick generator: `InTicks`, `OutTicks`, `InOutTicks`. |
| `arrowbar3 arrow` | `None` | Arrowhead specifier: `Arrow3`, `Arrows3`, `BeginArrow3`. |
| `margin3 margin` | `NoMargin3` | Clipping margins at axis endpoints. |
| `bool above` | `false` | Draws axis on top of (`true`) or underneath (`false`) 3D surfaces. |
| `projection P` | `currentprojection` | Projection used to align ticks and compute view-facing labels. |

### 3D Tick Parameters (`InTicks`, `OutTicks`, `InOutTicks`)
| Parameter | Default | Effect |
|---|---|---|
| `Label format` | `""` | Tick label format string (e.g. `"$%g$"`, `trailingzero`) or custom `Label`. |
| `int N` | `0` | Number of major intervals between ticks across axis (autoscale mode). |
| `int n` | `0` | Number of minor subintervals per major interval. |
| `real Step` | `0` | Explicit numeric interval between major ticks (used when $N=0$). |
| `real step` | `0` | Explicit numeric interval between minor ticks (used when $n=0$). |
| `bool begin, end` | `true, true` | Draw major ticks at initial/final axis endpoints. |
| `bool beginlabel, endlabel` | `true, true` | Draw tick labels at initial/final major ticks. |
| `real Size, size` | `0, 0` | Major / minor tick lengths in PostScript points (bp); 0 = default size. |
| `bool extend` | `false` | Extends tick marks across bounding box (creates coordinate grid). |
| `pen pTick, ptick` | `nullpen, nullpen` | Pens for major and minor tick marks. |

### 3D Grid Parameters (`grid3`)
| Parameter | Default | Effect |
|---|---|---|
| `grid3routinetype gridroutine` | `XYZgrid` | Grid layout: `XYZgrid`, `XYgrid`, `XZgrid`, `YZgrid`, `XYXgrid`, etc. |
| `position pos` | `Relative(0)` | Plane position along third axis (e.g. `pos=Relative(0)` for bottom plane). |
| `int N, n` | `0, 0` | Major and minor interval divisions across grid planes. |
| `real Step, step` | `0, 0` | Explicit step intervals between major and minor grid lines. |
| `pen pGrid` | `grey` | Pen used for major grid lines. |
| `pen pgrid` | `lightgrey` | Pen used for minor grid lines. |
| `bool above` | `false` | Draws grid lines on top of (`true`) or behind (`false`) scene objects. |

## Traps

1. **`currentprojection` is assigned, NOT passed**: Projections are set as a global variable: `currentprojection = perspective(5, 4, 2);` or `currentprojection = orthographic(X+2Y+3Z);`. Passing a projection to `draw()` or `size()` has no effect on the camera.
2. **`Label3` does NOT exist in Asymptote**: There is NO `Label3` struct or function. Asymptote unifies 2D and 3D labels under the standard `Label` type. Use `Label(XY()*"$x$", align=-3Y)` or `label("$z$", (0,0,1))` directly.
3. **3D labels face camera by default (`Billboard`)**: Text labels default to `Billboard` interaction (interactively rotating to face the user). To lay a label flat inside a coordinate plane, multiply the label string by a plane transform (`XY()*"label"`, `XZ()*"label"`) or set `interaction=Embedded`.
4. **Transform application order is right-to-left**: `T1 * T2 * point` applies `T2` first, then `T1`. For example, `shift(Z) * rotate(90, X)` rotates about the origin first, then shifts upward by Z.
5. **Pure vector output (`-render 0`) has limited hidden-surface removal**: With `settings.render=0`, Asymptote outputs pure 2D vector PostScript/PDF by projecting polygons. Hardware depth buffering is disabled; back-to-front sorting works reliably only for piecewise planar surfaces (fine `graph3` meshes, `solids` with large `nslice`). For exact planar scenes with overlap, use the `bsp` module.
6. **PRC PDF export requires declaration BEFORE `import three;`**: Setting `settings.prc = true;` and `settings.outformat = "pdf";` MUST occur before `import three;` or `import graph3;`. Setting it afterward fails silently or produces rasterized output.
7. **Thick 3D lines are rendered as `tube` surfaces**: Drawing lines with `linewidth > 0` in 3D automatically constructs cylinder tube surfaces. In complex scenes this causes high triangle counts and slow rendering. Use `settings.thick = false;` or the `thin()` pen to force fast 1-pixel vector lines.
8. **Default lighting alters pure colors**: `currentlight = Headlamp` illuminates surfaces by default, adding diffuse and specular highlights that alter pen colors. For unshaded, exact colors (e.g. flat diagrams or colormaps), pass `light=nolight` to `draw()`.
9. **Transparent canvas in WebGL**: HTML 3D outputs default to a white background. Set `currentlight.background = black + opacity(0.0);` to produce fully transparent WebGL viewports.
10. **Bounds axis edges & tick directions**: `Bounds(Min, Min)` draws bounding box edges at minimal coordinates. Non-Bounds axes (`YZZero`, etc.) inherit the tick directions that would be used for `Bounds(Min, Min)`.
11. **3D Arc normal vector requirement**: In `arc(c, v1, v2, normal=O)`, if the center `c` and points `v1, v2` are collinear, `cross(v1-c, v2-c)` is zero and the normal cannot be deduced automatically. The `normal` argument MUST be provided explicitly.
12. **`&` connector strips duplicate juncture**: The `&` path concatenation operator strips the trailing node of the first path and assumes it coincides with the initial node of the second path. If the points differ, `&` draws an unwanted straight jump.
13. **Data coordinates vs canvas coordinates**: When autoscaling or log scaling is enabled via `scale(Linear, Linear, Log)`, paths and manual annotations must be transformed with `Scale((x, y, z))` before placing labels or dots.

## Compile-gated examples

### Example 1: Bounded 3D surface plot with lighting, mesh lines & plane-aligned axis labels
```asy
import graph3;
size(200, 150, IgnoreAspect);
currentprojection = perspective(camera=(4, 5, 3), target=(0, 0, 0.5));

real f(pair z) { return cos(pi * z.x) * cos(pi * z.y); }
surface s = surface(f, (-1, -1), (1, 1), nx=10, Spline);

draw(s, paleblue, meshpen=gray(0.2)+linewidth(0.4), nolight, render(merge=true));

scale(true);
xaxis3(Label(XZ()*"$x$", align=-3Y), Bounds(Min, Min), red, InTicks(Label, 2, 2));
yaxis3(Label(YZ()*"$y$", align=-3X), Bounds(Min, Min), red, InTicks(beginlabel=false, Label, 2, 2));
zaxis3(Label(XZ()*"$z$", align=3X), Bounds(Min, Min), red, InTicks);
```

### Example 2: 3D parametric helix with mixed guide3 connectors, arrows & coordinate axes
```asy
import graph3;
size(200);
currentprojection = orthographic(4, 6, 3);

// 3D parametric curve
real x(real t) { return cos(2pi * t); }
real y(real t) { return sin(2pi * t); }
real z(real t) { return 0.5 * t; }
path3 p = graph(x, y, z, 0, 2.5, operator ..);
draw(p, blue + linewidth(1.5), Arrow3(size=6));

// Guide3 with mixed connectors: --, .., ::, ---, &
triple[] pt = {(0,0,0), (1,0,0), (1,1,0), (1,1,1), (0,1,1), (0,0,1)};
path3 poly = pt[0]--pt[1]..tension atleast 1.5..pt[2]---pt[3]::{Z}pt[4]&pt[4]..pt[5];
draw(poly, deepgreen + dashed);

xaxis3(Label(XY()*"$x$", align=-3Y), red, Arrow3);
yaxis3(Label(XY()*"$y$", align=-3X), red, Arrow3);
zaxis3(XYZero(extend=true), red, Arrow3);
```

### Example 3: Solids module — surfaces of revolution, spheres, cones & wireframe skeletons
```asy
import solids;
size(0, 150);
currentprojection = perspective(5, 4, 3);

// Predefined solids of revolution
revolution cyl = cylinder(O, 0.8, 1.5, Z);
revolution sph = sphere((2, 0, 0.8), 0.8);
revolution con = cone((0, 2, 0), 0.8, 1.5, Z);

// Draw shaded solid surfaces
draw(surface(cyl), paleblue + opacity(0.8));
draw(surface(sph), lightgreen + opacity(0.8));
draw(surface(con), yellow + opacity(0.8));

// Draw wireframe skeletons with separated front/back pens
draw(cyl, m=4, frontpen=blue, backpen=blue+dotted);
draw(sph, m=4, frontpen=deepgreen, backpen=deepgreen+dotted);
```

### Example 4: 3D coordinate grid with logarithmic Z-axis & binary space partition (BSP)
```asy
import grid3;
size(8cm, 0, IgnoreAspect);
currentprojection = orthographic(0.5, 1, 0.5);

scale(Linear, Linear, Log);
limits((-2, -2, 1), (0, 2, 100));

grid3(XYZgrid, pGrid=gray(0.7), pgrid=gray(0.9));
xaxis3(Label("$x$", position=EndPoint, align=S), Bounds(Min, Min), OutTicks());
yaxis3(Label("$y$", position=EndPoint, align=S), Bounds(Min, Min), OutTicks());
zaxis3(Label("$z$", position=EndPoint, align=(-1, 0.5)), Bounds(Min, Min), OutTicks(beginlabel=false));
```
