# Worked example figures (manual tutorials + module recipes)

> Read this when starting a new figure and wanting a proven skeleton. Each example compile-gates on this machine (`asy -f pdf`). Steal the pattern.

## 1. Labeled triangle with angle arc (plain drawing)

Teaches: path construction `--`+`cycle`, labels with compass directions, arcs, fills.

```asy
size(200);
pair A=(0,0), B=(4,0), C=(1,3);
draw(A--B--C--cycle);
label("$A$",A,SW); label("$B$",B,SE); label("$C$",C,N);
draw(anglemark(B,A,C));  // right-angle/angle marks
label("$\alpha$",A+(1,0.3));
fill(A--B--C--cycle,paleblue);
```

## 2. Function plot with axes (graph module)

Teaches: `graph(f,a,b)`, axis signatures, tick objects, LaTeX label.

```asy
import graph;
size(300,200,IgnoreAspect);
real f(real x) {return exp(-x^2);}
draw(graph(f,-3,3),blue);
xaxis("$x$",BottomTop,LeftTicks);
yaxis("$y$",LeftRight,RightTicks);
label("$e^{-x^2}$",(1,0.5),W);
```

## 3. Log-scale plot + legend

Teaches: scales, `Scale`, legend.

```asy
import graph;
size(300,200);
scale(Log,Log);
draw(graph(new real(real x){return x^2;},1,100),blue);
draw(graph(new real(real x){return x^3;},1,100),red);
xaxis("$x$",BottomTop,LeftTicks);
yaxis("$y$",LeftRight,RightTicks);
attach(legend(),point(E),20E,UnFill);
```

## 4. Contour plot (contour module)

Teaches: level sets, per-level pens via `sequence`.

```asy
import graph; import contour;
size(200);
real f(real x, real y) {return x^2-y^2;}
real[] c={-2,-1,0,1,2};
draw(contour(f,(-2,-2),(2,2),c));
```

## 5. Density image + palette bar (palette module)

Teaches: `image()`, bounds, palette bar with formatted ticks.

```asy
import graph; import palette;
size(10cm,10cm,IgnoreAspect);
real f(real x, real y) {return cos(x)*sin(y);}
pen[] Palette=BWRainbow();
bounds range=image(f,Automatic,(0,0),(pi,pi),100,Palette);
xaxis("$x$",BottomTop,LeftTicks,above=true);
yaxis("$y$",LeftRight,RightTicks,above=true);
palette("$f(x,y)$",range,point(SE)+(0.5,0),point(NE)+(1,0),Right,Palette,
        PaletteTicks("$%+#0.1f$",N=5));
```

## 6. 3D parametric surface (graph3 module)

Teaches: surface parametrization, projection, vector output via `settings.render=0`. The parametric `surface(f,a,b,nu,nv)` overloads live in **graph3** — importing only `three` gives "no matching function". Without `render=0` a headless/batch run rasterizes (slow, needs OpenGL); `render=0` outputs pure vector but converts labels through the latex+dvips+gs outline pipeline.

```asy
import graph3;
import settings;
settings.render=0;
size(300);
currentprojection=orthographic(5,4,2);
triple f(pair t) {
  real r=t.x, th=t.y;
  return (r*cos(th), r*sin(th), r^2);
}
draw(surface(f,(0,0),(2,2*pi),8,32),green,render(merge=true));
label("$z=r^2$",(1.5,1,2.5),N);
```

## 7. Flowchart (flowchart module)

Teaches: block shapes + the REQUIRED deferred connector wrapper.

```asy
import flowchart;
size(0,300);
block b1=rectangle("Start",(-0.5,3),palegreen,red);
block b2=diamond(Label("Choice?",blue),(0,2),palegreen,red);
block b3=roundrectangle("Do something",(-1,1));
block b4=bevel("Don't", (1,1));
block b5=circle("End",(0,0));
draw(b1); draw(b2); draw(b3); draw(b4); draw(b5);
add(new void(picture pic, transform t) {
  blockconnector operator --=blockconnector(pic,t);
  b1--Right--Down--Arrow--b2;
  b2--Label("Yes",0.5,NW)--Left--Down--Arrow--b3;
  b2--Right--Label("No",0.5,NE)--Down--Arrow--b4;
  b4--Down--Left--Arrow--b5;
  b3--Down--Right--Arrow--b5;
});
```

## 8. Hatched fill (patterns)

Teaches: pattern fills.

```asy
import patterns;
size(200);
add("hatch",hatch(2mm,NE));
filldraw(box((0,0),(3,2)),pattern("hatch"));
```

## Patterns to remember

- One `size()` at top; everything in user coordinates after.
- Draw order = paint order; `layer()` to sandwich (e.g. image under grid).
- Labels last, always with explicit direction.
- Reused geometry → functions returning `path`: `path bracket(pair a, pair b){...}`.
- Loop-built splines: accumulate into `guide`, not `path` (performance).
