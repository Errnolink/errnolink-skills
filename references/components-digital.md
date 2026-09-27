# Digital components (§4.22–§4.26, pp. 187–221)
> Read this when drawing: digital logic gates (American, IEEE standard, European), flip-flops, multiplexers/demultiplexers, custom IC packages (DIP, QFP), and seven-segment displays.

## Syntax recap
Verbatim canonical example from manual (tutorial §2.4, p. 32) — note `sr-ff` is a style the tutorial defines first (predefined ones: `flipflop SR`, `flipflop D`, `flipflop JK`, `flipflop T`, `latch`):
```latex
\tikzset{sr-ff/.style={flipflop, flipflop def={
  t1=S, t2=CP, t3=R, t4={\ctikztextnot{Q}}, t6=Q, nd=1}}}
```
```latex
\begin{circuitikz}
\draw (0,0) node[sr-ff](FF){} (FF.bup)
  node[above]{SR-FF} (FF.pin 1) -- ++(-1,0)
  node[and port, anchor=out](AND1){}
  (FF.pin 3) -- (FF.pin 3 -| AND1.out) node[and port, anchor=out](AND2){}
  (AND1.in 1) to[short, -*] ++(-1,0) coordinate(in)
  to[inline not] (in |- AND2.in 2) -- (AND2.in 2);
\end{circuitikz}
```
Orientation:
- Digital devices are primarily node-style multipoles (`node[<name>](<id>){<text>}`) rather than path bipoles, except for 1-in/1-out inline gates (`to[inline not]`, `to[inline buffer]`, `to[inline tgate]`).
- Gate families: `americanports` is default (`and port` = `american and port`). Use `\ctikzset{logic ports=ieee}` to map shorthands to IEEE standard distinctive shapes, or `europeanports` for IEC rectangular symbols.
- Gates support multi-input expansion (`number inputs=N`), input lead suppression (`no input leads`), and negated inputs via border anchors (`bin 1` with `\node at (.bin 1) [ocirc, left]{}`).
- Multi-pin modules are parametric: `flipflop` (pin text `t1..t6`, clock wedges `c1..c6`, negations `n1..n6`), `muxdemux` (heights `Lh`/`Rh`, width `w`, pin counts `NL`/`NR`/`NB`/`NT`, rich `muxdemux label` DSL), and `dipchip`/`qfpchip` (`num pins`, `hide numbers`, pad fractions).
- Displays use `bare7seg` with high-level style keys (`seven segment val` / `seven segment bits`).

## Component inventory

State fill-variants (`*`=full, `-`=stroke, `o`=empty) do not apply to digital multipoles; fill color is set via `logic ports/fill`, `muxdemuxes/fill`, or standard TikZ `fill=<color>`.

### American logic gates (§4.22.1, §4.22.5)
Shorthands (`and port`, `or port`, etc.) map to these when `americanports` is active (default).

| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `american and port` | `and port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | Standard 2-input AND; supports `number inputs` |
| `american or port` | `or port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | Pointy shape since v1.0.0 (`american or shape=pointy`) |
| `american nand port` | `nand port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | AND with inverting output bubble |
| `american nor port` | `nor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | OR with inverting output bubble |
| `american xor port` | `xor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | Curved detached input bar |
| `american xnor port` | `xnor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | XOR with inverting output bubble |
| `american buffer port` | `buffer port` | `node[x]` | `logic ports` | `in`, `in 1`, `out`, `bin`, `bin 1`, `bout`, `left`, `right`, `center` | Non-inverting single buffer (`in` and `in 1` identical) |
| `american not port` | `not port` | `node[x]` | `logic ports` | `in`, `in 1`, `out`, `bin`, `bin 1`, `bout`, `left`, `right`, `center` | Inverter (`in` and `in 1` identical) |
| `schmitt` | `schmitt port` | `node[x]` | `logic ports` | `in`, `in 1`, `out`, `bin`, `bin 1`, `bout`, `left`, `right`, `center` | Non-inverting Schmitt trigger; shared across styles |
| `invschmitt` | `invschmitt port` | `node[x]` | `logic ports` | `in`, `in 1`, `out`, `bin`, `bin 1`, `bout`, `left`, `right`, `center` | Inverting Schmitt trigger; shared across styles |

### IEEE standard logic gates (§4.22.2, §4.22.6)
Activated with `\ctikzset{logic ports=ieee}`. Shapes follow IEEE Std 91a-1991 proportions and maintain chip-pitch spacing.

| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `ieeestd and port` | `and port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `body left`, `body right`, `up`, `down` | Default 2-input height (2) matches `dipchip` pin pitch |
| `ieeestd nand port` | `nand port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `body left`, `body right`, `up`, `down`, `<name>-not` | Negation circle exposed as `<name>-not` node |
| `ieeestd or port` | `or port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `body left`, `body right`, `up`, `down` | Fixed IEEE curved aspect ratio |
| `ieeestd nor port` | `nor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `body left`, `body right`, `up`, `down`, `<name>-not` | Negated output on `bout` |
| `ieeestd xor port` | `xor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `bin 1`, `bin 2`, `ibin 1`, `ibin 2`, `out`, `bout`, `body left`, `body right` | `ibin` anchors sit on the inside edge of curved bar |
| `ieeestd xnor port` | `xnor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `bin 1`, `bin 2`, `ibin 1`, `ibin 2`, `out`, `bout`, `body left`, `<name>-not` | XOR with negation circle on output |
| `ieeestd buffer port` | `buffer port` | `node[x]` | `logic ports` | `in`, `bin`, `out`, `bout`, `body left`, `body right`, `up`, `down` | Smaller than multi-input gates; align via `anchor=in` |
| `ieeestd not port` | `not port` | `node[x]` | `logic ports` | `in`, `bin`, `out`, `bout`, `body left`, `body right`, `up`, `down`, `<name>-not` | Inverter; `component text=left` centers text visually |
| `ieeestd schmitt port` | `schmitt port` | `node[x]` | `logic ports` | `in`, `bin`, `out`, `bout`, `body left`, `body right`, `up`, `down` | Buffer with internal Schmitt hysteresis symbol |
| `ieeestd invschmitt port` | `invschmitt port` | `node[x]` | `logic ports` | `in`, `bin`, `out`, `bout`, `body left`, `body right`, `up`, `down`, `<name>-not` | Inverting Schmitt trigger matched to IEEE family |
| `ieee tgate` | `tgate` | `node[x]` | `logic ports` | `in`, `bin`, `out`, `bout`, `gate`, `bgate`, `notgate`, `bnotgate`, `up`, `down` | Single transmission gate (bowtie); control on `gate`/`notgate` |
| `ieee double tgate` | `double tgate` | `node[x]` | `logic ports` | `in`, `bin`, `out`, `bout`, `gate`, `bgate`, `notgate`, `bnotgate`, `up`, `down` | Dual complementary transmission gate |
| `notcirc` | — | `node[x]` | `logic ports` | `west`, `east`, `center` | Helper node: standalone negation bubble for input pins |
| `schmitt symbol` | — | `node[x]` | `logic ports` | `center`, `north west`, `south east` | Helper node: hysteresis icon for input lead decoration |

### European logic gates (§4.22.3, §4.22.7)
Shorthands map to these when `europeanports` is active. Standard symbols do not rotate/flip text automatically.

| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `european and port` | `and port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | Box with internal `&` symbol |
| `european or port` | `or port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | Box with internal `\ge 1` symbol |
| `european nand port` | `nand port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | `&` box with output negation marker (triangle/circle) |
| `european nor port` | `nor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | `\ge 1` box with output negation marker |
| `european xor port` | `xor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | Box with internal `= 1` symbol |
| `european xnor port` | `xnor port` | `node[x]` | `logic ports` | `in 1`, `in 2`, `out`, `bin 1`, `bin 2`, `bout`, `left`, `right`, `center` | Box with `= 1` and negated output, or `= ` if `direct` |
| `european buffer port` | `buffer port` | `node[x]` | `logic ports` | `in`, `in 1`, `out`, `bin`, `bin 1`, `bout`, `left`, `right`, `center` | Box with internal `1` symbol |
| `european not port` | `not port` | `node[x]` | `logic ports` | `in`, `in 1`, `out`, `bin`, `bin 1`, `bout`, `left`, `right`, `center` | Box with `1` and output negation marker |
| `european blank port` | `blank port` | `node[x]` | `logic ports` | `in 1..N`, `out`, `bin 1..N`, `bout`, `left`, `right`, `center` | Empty box; accepts custom inner text `node[...]{text}` |
| `european blank not port` | `blank not port` | `node[x]` | `logic ports` | `in 1..N`, `out`, `bin 1..N`, `bout`, `left`, `right`, `center` | Empty box with output negation mark for custom text |

### Path-style inline logic ports (§4.22.4)
One-input, one-output path bipoles. Follow active family style. Support `name=<id>` (or `<name>=<id>`), `mirror`, `invert`, `l=`, `a=`, `v=`.

| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `inline not` | — | `to[x]` path | `logic ports` | Standard bipole anchors; border anchors on nodename (`not port`) | Nodename `not port`; assigned value typesets node text |
| `inline buffer` | — | `to[x]` path | `logic ports` | Standard bipole anchors; border anchors on nodename (`buffer port`) | Nodename `buffer port` |
| `inline schmitt` | — | `to[x]` path | `logic ports` | Standard bipole anchors; border anchors on nodename (`schmitt port`) | Nodename `schmitt port` |
| `inline invschmitt` | — | `to[x]` path | `logic ports` | Standard bipole anchors; border anchors on nodename (`invschmitt port`) | Nodename `invschmitt port` |
| `inline tgate` | — | `to[x]` path | `logic ports` | `bgate`, `bnotgate` on named node (`name=P`) | Nodename `tgate`; no control pins drawn inline; wire to `bgate` |
| `inline double tgate` | — | `to[x]` path | `logic ports` | `bgate`, `bnotgate` on named node (`name=P`) | Nodename `double tgate`; wire controls to `bgate`/`bnotgate` |

### Flip-flops (§4.23)
Hybrid logic/chip rectangular boxes (class `flipflops`). Pins 1..6 lateral (DIP order: 1-3 left top-to-bottom, 4-6 right bottom-to-top), `up`/`down` for top/bottom.

| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `flipflop` | — | `node[x]` | `flipflops` | `pin 1..6`, `up`, `down`, `bpin 1..6`, `bup`, `bdown`, `<name>-N<pin>` | Void 6-pin box; configure pins via `flipflop def` |
| `latch` | — | `node[x]` | `flipflops` | `pin 1` (D), `pin 3` (CLK), `pin 4` ($\bar{\text{Q}}$), `pin 6` (Q) | Transparent D latch (`t1=D, t6=Q, t3=CLK, t4=\ctikztextnot{Q}`) |
| `flipflop SR` | — | `node[x]` | `flipflops` | `pin 1` (S), `pin 3` (R), `pin 4` ($\bar{\text{Q}}$), `pin 6` (Q) | Async SR latch (`t1=S, t3=R, t6=Q, t4=\ctikztextnot{Q}`) |
| `flipflop D` | — | `node[x]` | `flipflops` | `pin 1` (D), `pin 3` (CLK wedge), `pin 4` ($\bar{\text{Q}}$), `pin 6` (Q) | Synchronous D flip-flop (`t1=D, t6=Q, c3=1, t4=\ctikztextnot{Q}`) |
| `flipflop T` | — | `node[x]` | `flipflops` | `pin 1` (T), `pin 3` (CLK wedge), `pin 4` ($\bar{\text{Q}}$), `pin 6` (Q) | Synchronous T flip-flop (`t1=T, t6=Q, c3=1, t4=\ctikztextnot{Q}`) |
| `flipflop JK` | — | `node[x]` | `flipflops` | `pin 1` (J), `pin 2` (CLK wedge), `pin 3` (K), `pin 4` ($\bar{\text{Q}}$), `pin 6` (Q) | Synchronous JK flip-flop (`t1=J, t3=K, c2=1, t6=Q, t4=\ctikztextnot{Q}`) |
| `clockwedge` | — | `node[x]` | `flipflops` | `center`, `nw`, `sw`, `right`, `text` | Standalone wedge shape for custom clock inputs |

Predefined flip-flop add-on styles:
- `add async SR`: adds top active-low SET (`tu={\ctikztextnot{SET}}`) and bottom active-low RST (`td={\ctikztextnot{RST}}`).
- `dot on notQ`: replaces $\bar{\text{Q}}$ text with `Q` and sets negation flag on pin 4 (`t4={Q}, n4=1`).

### Multiplexers and de-multiplexers (§4.24)
Parametric trapezoidal block (class `muxdemuxes`). Pin leads stick out from left/right/bottom/top.

| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `muxdemux` | — | `node[x]` | `muxdemuxes` | `lpin 1..NL`, `rpin 1..NR`, `bpin 1..NB`, `tpin 1..NT`, `blpin..`, `brpin..`, `bbpin..`, `btpin..`, `inset...` | Fully parametric 8-to-1 MUX default (`Lh=8, Rh=6, w=3, NL=8, NR=1, NB=3, NT=0`) |
| `demux` | — | `node[x]` | `muxdemuxes` | `lpin 1`, `rpin 1..8`, `bpin 1..3`, border variants | Predefined 1-to-8 DEMUX (`Lh=4, Rh=8, NL=1, NB=3, NR=8`) |
| `one bit adder` | — | `node[x]` | `muxdemuxes` | `lpin 1..2`, `rpin 1`, `bpin 1`, border variants, `inset...` | Predefined adder with trapezoid inset (`Lh=4, NL=2, Rh=2, NR=1, NB=1, w=1.5, inset w=0.5, inset Lh=2, inset Rh=1.5`) |
| `ALU` | — | `node[x]` | `muxdemuxes` | `lpin 1..2`, `rpin 1`, `bpin 1..2`, `tpin 1`, `inset...` | Predefined ALU shape with V-notch inset (`Lh=5, NL=2, Rh=2, NR=1, NB=2, NT=1, w=2, inset w=1, inset Lh=2, square pins=1`) |
| `wedgeinv` | — | `node[x]` | `muxdemuxes` | `base`, `top`, `apex`, `midcenter` | Standalone European triangle negation symbol; default anchor `left` |
| `circleinv` | — | `node[x]` | `muxdemuxes` | `base`, `top`, `apex`, `bottom`, `midcenter` | Standalone circle negation marker; scales with `muxdemuxes` |

### Integrated circuit chips (§4.25)
Standard dual and quad flat IC packages (class `chips`). Numbered counterclockwise starting at top-left.

| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `dipchip` | — | `node[x]` | `chips` | `pin 1..N`, `bpin 1..N`, `n`, `s`, `e`, `w`, `nw`, `ne`, `sw`, `se`, `dot`, `text`, `center` | Dual-in-Line Package; `num pins` MUST be even |
| `qfpchip` | — | `node[x]` | `chips` | `pin 1..N`, `bpin 1..N`, `n`, `s`, `e`, `w`, `nw`, `ne`, `sw`, `se`, `dot`, `text`, `center` | Quad-Flat Package; `num pins` MUST be multiple of 4 |

### Seven-segment displays (§4.26)
Standard 7-segment LED display (class `displays`).

| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `bare7seg` | — | `node[x]` | `displays` | `a`, `b`, `c`, `d`, `e`, `f`, `g`, `dot`, `north`, `south`, `east`, `west`, `center` | Unconfigured 7-segment digit; configure via `val` or `bits` styles |

Display configuration styles:
- `seven segment val=<0..15|A..F|-> dot <none|empty|on|off> box <on|off>`: sets hex digit or minus.
- `seven segment bits=<7-bits> dot <none|empty|on|off> box <on|off>`: sets exact segment on/off mask (`abcdefg`).

## Family keys (\ctikzset)

### Logic gates keys (§4.22)
`logic ports=american|european|ieee` → select active gate shape family and shorthand mapping
`logic ports/scale` → scale factor for all logic gates (default `1.0`)
`logic ports/fill` → fill color for all logic gates (default `none`)
`logic ports origin=center` → set gate `center` anchor to the true geometric center
`logic ports draw leads=true|false` → master switch to draw or suppress all gate leads
`logic ports draw input leads=true|false` → draw or suppress gate input leads (styles: `no input leads`, `input leads`)
`logic ports draw output leads=true|false` → draw or suppress gate output leads (styles: `no output leads`, `output leads`)
`american or shape=pointy|roundy` → choose pointy (post-v1.0.0 default) or roundy (legacy) American OR-family shape
`tripoles/thickness` → border line thickness for tripoles and gates (default `2`)
`tripoles/american <type> port/port width` → body width of American port (default `0.7`)
`tripoles/american <type> port/height` → body height of American port (default varies)
`tripoles/american <type> port/input height` → vertical span of inputs for American port
`tripoles/american xnor port/inner` → circle indentation for American OR family curve (default `0.3`)
`tripoles/american xnor port/angle` → opening angle of American OR base curve (default `70`)
`number inputs` → number of input pins on multi-input gates
`inner inputs` → number of pins on IEEE gate rack before extending leads
`component text=left|center` → position node label near left border (works for IEEE ports and amplifiers)
`ieeestd ports/baselen` → base unit length for IEEE port dimensions (default `0.4`)
`ieeestd ports/height` → height of IEEE port in `baselen` units (default `2`); pin pitch = `height / inner inputs`
`ieeestd ports/pin length` → length of external input/output leads for IEEE ports (default `0.7`)
`ieeestd ports/not radius` → radius of negation circle on IEEE gates (default `0.154`)
`ieeestd ports/xor bar distance` → spacing of detached input curve on IEEE XOR/XNOR gates (default `0.192`)
`ieeestd ports/xor leads in=1|0` → whether input leads cross detached bar to body (`1` = yes, `0` = no)
`ieeestd ports/schmitt symbol size` → size of hysteresis icon added to IEEE input leads (default `0.3`)
`tgate scale` → relative scaling factor for transmission gate triangles (default `0.7`)
`european ports font` → font and color override for text inside European gates (default empty)
`tripoles/european not symbol=triangle|circle|ieee circle` → inverter mark style on European gates (default `triangle`)
`european xnor style=default|direct` → European XNOR label symbol (`= 1` with invert vs just `=`)

### Flip-flop keys (§4.23)
`flipflops/scale` → scale factor for all flip-flop components (default `1.0`)
`multipoles/flipflop/width` → width of flip-flop box relative to basic length (default `1.2`)
`multipoles/flipflop/pin spacing` → vertical spacing between lateral pins (default `0.4`)
`multipoles/flipflop/font` → font for lateral pin labels 1..6 (default `\small`)
`multipoles/flipflop/fontud` → font for vertical up/down pin labels (default `\tiny`)
`multipoles/flipflop/clock wedge size` → relative size of clock wedge triangle (default `0.2`)
`multipoles/flipflop/t0..t6,tu,td` → pin label text for pins 1..6 and up/down
`multipoles/flipflop/c0..c6,cu,cd` → clock wedge flag (`1` = draw wedge, `0` = none)
`multipoles/flipflop/n0..n6,nu,nd` → negation circle flag (`1` = draw ocirc, `0` = none)
`flipflop def={...}` → style shorthand to define flip-flop pin attributes (`t1..t6`, `c1..c6`, `n1..n6`)
`add async SR` → style adding active-low asynchronous SET (`tu`) and RST (`td`)
`dot on notQ` → style changing pin 4 label to `Q` and adding negation circle `n4=1`

### Mux-demux keys (§4.24)
`muxdemuxes/scale` → scale factor for muxdemux components (default `1.0`)
`muxdemuxes/thickness` → border line thickness for muxdemuxes (default `2`)
`muxdemuxes/fill` → fill color for muxdemuxes (default `none`)
`multipoles/muxdemux/base len` → base unit length for muxdemux dimensions (default `0.4`)
`multipoles/muxdemux/Lh` → left height of muxdemux in base lengths (default `8`)
`multipoles/muxdemux/Rh` → right height of muxdemux in base lengths (default `6`)
`multipoles/muxdemux/w` → width of muxdemux in base lengths (default `3`)
`multipoles/muxdemux/NL` → number of pins on left side (default `8`)
`multipoles/muxdemux/NR` → number of pins on right side (default `1`)
`multipoles/muxdemux/NB` → number of pins on bottom side (default `3`)
`multipoles/muxdemux/NT` → number of pins on top side (default `0`)
`multipoles/muxdemux/inset Lh` → vertical extent of left-side trapezoidal inset (default `0`)
`multipoles/muxdemux/inset Rh` → vertical extent of right-side trapezoidal inset (default `0`)
`multipoles/muxdemux/inset w` → horizontal depth of inset (default `0`)
`multipoles/muxdemux/square pins=0|1` → pins stick out normal to slope (`0`) or squarely vertical (`1`)
`muxdemux def={...}` → style shorthand for defining muxdemux geometry (`Lh`, `Rh`, `w`, `NL`, `NR`, `NB`, `NT`, `inset...`, `square pins`, `bgpicture`)
`muxdemux label={...}` → clause defining inner labels (`L1`, `R1`), outer labels (`LU1`, `TL1`), border labels (`N`, `Ni`), and symbols (`cL2`, `nL1`, `wiB1`, `woR1`)
`muxdemux/inner label font` → font for inside pin labels (default `\tiny`)
`muxdemux/inner label xsep` → horizontal padding for inner pin labels (default `2pt`)
`muxdemux/inner label ysep` → vertical padding for inner pin labels (default `2pt`)
`muxdemux/inner label sep` → horizontal and vertical padding for inner labels simultaneously
`muxdemux/outer label font` → font for outside pin labels (default `\tiny`)
`muxdemux/outer label xsep` → horizontal padding for outer pin labels (default `2pt`)
`muxdemux/outer label ysep` → vertical padding for outer pin labels (default `2pt`)
`muxdemux/outer label sep` → horizontal and vertical padding for outer labels simultaneously
`muxdemux/border label font` → font for border labels along N/S/E/W edges (default `\tiny`)
`muxdemux/border label xsep` → horizontal padding for border labels (default `2pt`)
`muxdemux/border label ysep` → vertical padding for border labels (default `2pt`)
`muxdemux/border label sep` → horizontal and vertical padding for border labels simultaneously
`muxdemux/clock wedge size` → relative size of clock wedge symbol on muxdemux pins (default `0.2`)
`wedge inversion mark/width` → width of standalone `wedgeinv` marker (default `0.2`)
`wedge inversion mark/height` → height of standalone `wedgeinv` marker (default `0.1`)
`multipoles/draw only <side> pins={<list>}` → comma-separated list or ranges of pins to draw on `<side>` (`left`, `right`, `top`, `bottom`)

### Chips keys (§4.25)
`chips/scale` → scale factor for chip packages (does not scale pin text)
`multipoles/thickness` → border line thickness of chip body (default `2`)
`multipoles/external pins thickness` → line thickness of external chip pins/pads (default `1`)
`multipoles/dipchip/width` → width of DIP chip body in basic lengths (default `1.2`)
`multipoles/dipchip/pin spacing` → pin spacing for DIP chip in basic lengths (default `0.4`)
`multipoles/qfpchip/pin spacing` → pin spacing for QFP chip in basic lengths (default `0.4`)
`num pins=<count>` → total pin count (must be even for DIP, multiple of 4 for QFP)
`multipoles/external pins width` → length of pin leads (default `0.2`; `0` puts pin anchors on border)
`multipoles/external pad fraction` → converts pin wire into surface pad of fraction width (default `0`)
`hide numbers` / `show numbers` → toggle automatic printing of pin numbers along leads (default `show numbers`)
`no topmark` / `topmark` → toggle orientation notch/semicircle at top of chip (default `topmark`)
`straight numbers` / `rotated numbers` → toggle counter-rotation of pin numbers when chip is rotated (default `straight numbers`)
`multipoles/font` → font used for chip pin numbers (default `\tiny`)
`multipoles/draw only pins={<list>}` → comma-separated list or ranges of pins to draw (e.g. `{1, 3, 5-8}`)

### Seven-segment display keys (§4.26)
`displays/scale` → scale factor for digit geometry in 7-segment displays
`seven seg/bits` → 7-character binary string controlling segments `abcdefg` (default `0000000`)
`seven seg/dot=none|empty|on|off` → dot mode: omitted (`none`), space reserved (`empty`), illuminated (`on`), unlit (`off`)
`seven seg/box=on|off` → toggle bounding border box (default `off`)
`seven seg/width` → segment width relative to basic length (default `0.4`, scalable)
`seven seg/thickness` → stroke thickness of segments (default `4pt`, unscaled)
`seven seg/segment sep` → physical gap between adjacent segments (default `0.2pt`, unscaled)
`seven seg/box sep` → separation gap between outer box and segments (default `1pt`, unscaled)
`seven seg/color on` → color of active segments (default `red`)
`seven seg/color off` → color of inactive segments (default `gray!20!white`)
`seven segment val=<val> dot <dot> box <box>` → style configuring digit from value (`0..15`, `A..F`, `-`)
`seven segment bits=<bits> dot <dot> box <box>` → style configuring digit from exact 7-bit string

## Traps

1. **`num pins` parity rules**: `dipchip` pin count MUST be even; `qfpchip` pin count MUST be a multiple of 4. Any violation throws cryptic low-level TeX errors ("havoc will ensue").
2. **`seven segment bits` length**: MUST be exactly 7 binary digits (e.g. `1001001`). Specifying fewer or more bits triggers an obscure parser crash.
3. **Chip and flip-flop pin anchor names**: pin anchors require a space between `pin` and the number, e.g. `(C.pin 1)` or `(FF.pin 3)`, NOT `(C.pin1)`. In contrast, border pin anchors have no space after `bpin`: `(C.bpin 1)`.
4. **Chip rotation vs text rotation**: rotating a chip (`node[dipchip, rotate=90]`) keeps pin numbers upright automatically under `straight numbers`, but the main node text (`{IC1}`) is NOT counter-rotated. You MUST counter-rotate the main label manually: `node[dipchip, rotate=90]{\rotatebox{-90}{IC1}}`.
5. **European gate symbol un-rotation**: internal symbols (`&`, `\ge 1`, `= 1`) do NOT rotate or flip with the component node (since v1.6.4). If using `european blank port`, manual node text must be counter-rotated with `\rotatebox`.
6. **Logic port `center` anchor**: for historical reasons, `(G.center)` is NOT at the visual center of American or European gates. Issue `\ctikzset{logic ports origin=center}` to align `center` with the true geometrical center.
7. **`no input leads` vs `external pins width=0`**: `no input leads` suppresses drawing input wires but leaves `in 1` anchors at the would-be lead tips. `external pins width=0` physically pulls pin anchors onto the component boundary so `pin 1` and `bpin 1` coincide.
8. **Negation circle connections on pinless chips/flip-flops**: negation circles (`ocirc`) are drawn last to blank out underlying lead wires. If `external pins width=0`, drawing to `(A.pin 4)` causes the wire to cut through the bubble; connect explicitly to the bubble's outer anchor `(A-N4.east)` or `(A-nL1.-135)`.
9. **IEEE ports are not active by default**: loading CircuiTikZ defaults to American gates. There is NO package option `[ieee]`; you MUST issue `\ctikzset{logic ports=ieee}` to map `and port`, `or port`, etc. to IEEE shapes.
10. **IEEE port pitch matching**: standard 2-input IEEE gates match `dipchip` pin pitch (0.4 basic lengths) by default. For multi-input gates, use `inner inputs=2` (rack mechanism) or adjust `ieeestd ports/height=N` to maintain DIP alignment. Avoid odd numbers on inner racks, as some inputs land awkwardly on the border curve.
11. **IEEE port minimum lead length**: setting `ieeestd ports/pin length` too short (less than body curve extrusions) causes visual overlap glitches on XOR/XNOR and multi-input gates.
12. **`muxdemux label={...}` silently ignores errors**: label keys (`L1`, `RU2`, `cL1`, etc.) are not validated against component pin counts or syntax; misspelled keys are silently ignored with no warning.
13. **`muxdemux` inset pin placement**: pins do NOT land inside the trapezoidal cutout; they distribute along the top and bottom exterior flanks (unless `NL` is odd, where one pin sits in the inset center).
14. **`muxdemux` square pins**: on slanted trapezoids (MUX/DEMUX), top and bottom pin leads stick out at an angle normal to the slope by default (`square pins=0`). To connect vertically to chips or orthogonal wires, MUST set `square pins=1`.
15. **Transmission gates inline control pins**: `inline tgate` and `inline double tgate` do not draw vertical control lead lines in path style. You must connect gate controls using border anchors `(name.bgate)` and `(name.bnotgate)`.
16. **`\ctikztextnot{}` usage**: flip-flop negated labels (e.g. `\ctikztextnot{Q}` or `\ctikztextnot{RST}`) require the CircuiTikZ macro `\ctikztextnot`, NOT LaTeX `\overline`, to guarantee proper bounding box calculation and height metrics.

### 555 timer using `muxdemux` (manual §4.24.7, p. 214)
Streamlined modern implementation (v1.6.5+) using `muxdemux def` and `muxdemux label`:
```latex
\begin{tikzpicture}[scale=0.7, transform shape]
\ctikzset{muxdemux/inner label font=\small}
\ctikzset{muxdemux/outer label font={\small\color{blue}}}
\tikzset{ic555/.style={muxdemux,
  muxdemux def={Lh=10, NL=5, Rh=10, NR=1, NB=2, w=6, NT=2},
  muxdemux label={L2=Discharge, L4=Trigger, L5=Threshold,
    T1=VCC, B2=GND,
    LU2=7, LU4=2, LU5=6, TL1=8, TL2=4, RU1=3, BL1=5, BL2=1},
  external pins width=0.4,
  draw only left pins={2,4,5},
  circuitikz/muxdemuxes/fill=blue!10}
}
\node [ic555, font=\small\ttfamily, align=center](A) at (0,0) {555\\Astable};
\end{tikzpicture}
```

Cross-checked against manual index: 56 names verified.
