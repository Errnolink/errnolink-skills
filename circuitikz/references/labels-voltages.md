# Labels, Voltages, Currents and Poles (ch. 5–6, ch. 8 FAQ, pp. 222–269)
> Read this when drawing: adding labels, annotations, voltages, currents, or flows to bipoles; configuring voltage direction policies (`RPvoltages` vs `EFvoltages`); selecting European, American, straight, or raised voltage styles; inserting terminal poles (`*-*`, `o-o`, `d-d`); styling text ornaments; accessing anchor nodes for custom arrows; or debugging wire gaps, colored/dashed wires, and externalization.

## Syntax recap
```latex
\begin{circuitikz}[voltage dir=RP, american]
    \draw (0,0) to[R=$R_1$, a=\SI{1}{\kohm}, v^<=$v_1$, i>_=$i_1$, f>^=$f_1$, *-o] (3,0);
\end{circuitikz}
```
- Path bipoles support five decorations: label `l`, annotation `a`, voltage `v`, current `i`, and flow `f`.
- Active vs passive shortcut: `to[R=R1]` sets label `l`, but `to[V=V1]` sets voltage `v`, and `to[I=I1]` sets current `i`.
- Position modifiers `^` (above/right) and `_` (below/left) control side; `<` and `>` control arrow direction.
- Modifier ordering: swapping modifier order (e.g. `i^>` vs `i>^`) flips placement from before to after the component.
- Direction policy (`RPvoltages` vs `EFvoltages`) must be declared at environment level, never per-component.
- Poles (`o-o`, `*-*`, `d-d`) add terminal nodes at endpoints; open poles mask underlying wires via background fill.

## Component & decoration inventory

### Five bipole decorations
| Decoration | Primary Key | Default Side | Active/Passive Shorthand | Associated Text Node | Advanced Anchors |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Label** | `l=<text>` | Above / right | `to[R=<text>]` (passives only) | `<name>label` | Inherited from bipole body |
| **Annotation** | `a=<text>` | Below / left | None (`a=` always required) | `<name>annotation` | Inherited from bipole body |
| **Voltage** | `v=<text>` | Above / across | `to[V=<text>]` (voltage sources) | `<name>voltage` | `Vfrom`, `Vto`, `Vcont1`, `Vcont2`, `Vlab` |
| **Current** | `i=<text>` | On wire | `to[I=<text>]` (current sources) | `<name>current` | `Ifrom`, `Ito`, `Ipos` |
| **Flow** | `f=<text>` | Beside body | None | `<name>flow` | `Ffrom`, `Fto`, `Fpos` |

### Position and direction modifier combinations
| Modifier | Decoration Placement | Arrow Direction | Position on Wire | Notes / Visual Result |
| :--- | :--- | :--- | :--- | :--- |
| `l=` / `l^=` | Above / right | None | Component center | Default label position |
| `l_=` | Below / left | None | Component center | Inverted side label |
| `a=` / `a_=` | Below / left | None | Component center | Default annotation position |
| `a^=` | Above / right | None | Component center | Inverted side annotation |
| `i^>` / `f^>` | Above / right | Forward (start $\rightarrow$ end) | **Before** component | Input wire segment |
| `i_>` / `f_>` | Below / left | Forward (start $\rightarrow$ end) | **Before** component | Input wire segment |
| `i^<` / `f^<` | Above / right | Backward (end $\rightarrow$ start) | **Before** component | Input wire segment |
| `i_<` / `f_<` | Below / left | Backward (end $\rightarrow$ start) | **Before** component | Input wire segment |
| `i>^` / `f>^` | Above / right | Forward (start $\rightarrow$ end) | **After** component | Output wire segment |
| `i>_` / `f>_` | Below / left | Forward (start $\rightarrow$ end) | **After** component | Output wire segment |
| `i<^` / `f<^` | Above / right | Backward (end $\rightarrow$ start) | **After** component | Output wire segment |
| `i<_` / `f<_` | Below / left | Backward (end $\rightarrow$ start) | **After** component | Output wire segment |
| `v^>` | Above / right | Forward | Across bipole | Curved/straight arrow forward, or `−` start `+` end |
| `v^<` | Above / right | Backward | Across bipole | Curved/straight arrow backward, or `+` start `−` end |
| `v_>` | Below / left | Forward | Across bipole | Arrow below forward, or `−` start `+` end |
| `v_<` | Below / left | Backward | Across bipole | Arrow below backward, or `+` start `−` end |

### Terminal pole shorthands (ch. 6)
Terminal shapes: `circ` (filled circle), `ocirc` (open circle), `diamondpole` (filled diamond), `odiamondpole` (open diamond), `squarepole` (filled square), `osquarepole` (open square), `rectfill`, `none`.
| Shorthand | Start Terminal | End Terminal | Full `bipole nodes` equivalent | Common Application |
| :--- | :--- | :--- | :--- | :--- |
| `o-o` | `ocirc` | `ocirc` | `bipole nodes={ocirc}{ocirc}` | Open-circuit ports, test terminals |
| `-o` | `none` | `ocirc` | `bipole nodes={none}{ocirc}` | Output port terminal |
| `o-` | `ocirc` | `none` | `bipole nodes={ocirc}{none}` | Input port terminal |
| `*-*` | `circ` | `circ` | `bipole nodes={circ}{circ}` | Wire junction nodes at both ends |
| `-*` | `none` | `circ` | `bipole nodes={none}{circ}` | Junction node at output end |
| `*-` | `circ` | `none` | `bipole nodes={circ}{none}` | Junction node at input end |
| `d-d` | `diamondpole` | `diamondpole` | `bipole nodes={diamondpole}{diamondpole}` | Solder/plug connectors at both ends |
| `-d` | `none` | `diamondpole` | `bipole nodes={none}{diamondpole}` | Connector at end |
| `d-` | `diamondpole` | `none` | `bipole nodes={diamondpole}{none}` | Connector at start |
| `o-*` | `ocirc` | `circ` | `bipole nodes={ocirc}{circ}` | External port to circuit junction |
| `*-o` | `circ` | `ocirc` | `bipole nodes={circ}{ocirc}` | Internal junction to external port |
| `o-d` | `ocirc` | `diamondpole` | `bipole nodes={ocirc}{diamondpole}` | Port to solder jumper |
| `d-o` | `diamondpole` | `ocirc` | `bipole nodes={diamondpole}{ocirc}` | Solder jumper to port |
| `*-d` | `circ` | `diamondpole` | `bipole nodes={circ}{diamondpole}` | Junction to solder header |
| `d-*` | `diamondpole` | `circ` | `bipole nodes={diamondpole}{circ}` | Solder header to junction |

## Label formatting, stacking, and parser rules

### Protecting `=` and `,` in values (the brace rule)
The TikZ key-val parser uses `=` and `,` as parameter delimiters. Any label or annotation value containing `=` or `,` MUST be protected in a grouped pair of curly braces `{...}`:
```latex
\draw (0,0) to[R={$R=3$}] (2,0);           % Correct
\draw (0,0) to[R, l={$R=3$}] (2,0);        % Correct
\draw (0,0) to[R={$R,3$}] (2,0);           % Correct
\draw (0,0) to[ospst={open, 3s}] (2,0);    % Correct
% \draw (0,0) to[R, l=$R=3$] (2,0);        % FAILS! Parser truncates at '='
```
*Legacy note*: In CircuiTikZ $\le 1.2.7$, option parsing required extra protection via `\mbox{$R=3$}` or `\def\eq{=}` with `$R\eq 3$`.

### Label distance & alignment
```latex
\ctikzset{label distance=2pt, annotation distance=-3pt}
\ctikzset{label/align=smart}               % Options: smart (default), rotate, straight
```
- `label/align=straight` (package option `straightlabels`): labels are strictly horizontal regardless of bipole angle.
- `label/align=rotate` (package option `rotatelabels`): labels rotate to follow the slope of the bipole path.
- `label/align=smart` (package option `smartlabels`): labels rotate along sloped wires, but remain horizontal for vertical/near-vertical lines to maintain readability.

### Stacked two-line labels (`l2`, `a2`)
Used to typeset name and value as a multi-line block without manual positioning:
```latex
\draw (0,0) to[R, l2_=$R_1$ and \SI{4.7}{\kohm}, l2 halign=c, l2 valign=b] ++(2,0);
\draw (3,0) to[R, l2={A=B} and X, a2={C=D} and Y] ++(0,-3);
```
- Key format: `l2=line1 and line2`, `l2_=...`, `l2^=...`; annotations: `a2=...`, `a2_=...`, `a2^=...`.
- Alignment keys: `l2 halign=l|c|r` (default `l`), `l2 valign=t|c|b` (default `c`).
- Annotation alignment: `a2 halign=l|c|r` (default `l`), `a2 valign=t|c|b`.
- **Engine limit**: `l2` and `a2` use LaTeX `tabular` internally; they fail in plain TeX and ConTeXt.
- **Trap**: The compact `siunitx` `<...>` notation CANNOT be used inside `l2` or `a2` (throws an error since v1.3.6). Always use explicit `\SI{}{}` or `\qty{}{}`.

### Compact `siunitx` syntax
When `circuitikz` is loaded with `[siunitx]`, angle brackets provide compact unit formatting:
```latex
\draw (0,0) to[R, l=1<\kilo\ohm>, i=1<\milli\ampere>, v=10<\volt>] (3,0);
```
- Expands internally to `\SI{1}{\kilo\ohm}` (or `\qty{1}{\kilo\ohm}` on `siunitx` v3).
- Works for `l`, `a`, `v`, `i`, and passive bipole shortcuts (`to[R=10<\kilo\ohm>]`).
- Incompatible with ConTeXt (disabled upstream). Incompatible with stacked labels `l2`/`a2`.

## Voltage & current direction conventions

Set direction policy globally at environment level (`\begin{circuitikz}[voltage dir=...]`):
1. `RPvoltages` (or `voltage dir=RP`) — **Recommended standard** (used across manual). Rising potential: arrows point toward increasing potential (+). Batteries and current sources are oriented to deliver active power by default.
2. `EFvoltages` (or `voltage dir=EF`) — Electric Field direction: arrows point from + to − (potential drop).
3. `nooldvoltagedirection` (or `voltage dir=noold`) — v0.5 convention; electric field arrows, uncorrected battery polarity.
4. `oldvoltagedirection` (or `voltage dir=old`) — Pre-v0.5 legacy behavior.

### Voltage & current default direction semantics
| Component Class | Direction Policy | Current arrow `i` | European voltage arrow `v` | American `+` sign | American `−` sign | Physical Meaning |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Passive** (`R, C, L, D`) | `RPvoltages` | Start $\rightarrow$ End | End $\rightarrow$ Start (opposes $i$) | Start terminal | End terminal | Dissipates power |
| **Passive** (`R, C, L, D`) | `EFvoltages` | Start $\rightarrow$ End | Start $\rightarrow$ End (along $i$) | Start terminal | End terminal | Potential drop |
| **Source** (`V, sV, cV, battery`) | `RPvoltages` | Start $\rightarrow$ End | Start $\rightarrow$ End (along $i$) | End terminal | Start terminal | Generates power |
| **Source** (`V, sV, cV, battery`) | `EFvoltages` | Start $\rightarrow$ End | End $\rightarrow$ Start (opposes $i$) | End terminal | Start terminal | Potential boost |

## Four voltage styles and positioning

Select via package option or inline key `voltage=<style>`:
1. `european` (`europeanvoltages` / `voltage=european`, default): curved arrow spanning the bipole body.
   - *Exception*: Voltage generators draw straight arrows even in European style for historical reasons.
2. `straight` (`straightvoltages` / `voltage=straight`): straight arrow drawn parallel to bipole path.
3. `american` (`americanvoltages` / `voltage=american`): `+` and `−` symbols placed near the terminals along the wire.
4. `raised` (`raised voltages` / `voltage=raised`): American style where `+` and `−` symbols are raised to the baseline of the voltage label (`+ [label] −`).

### Voltage positioning and fine-tuning
```latex
\ctikzset{voltage shift=0.5}               % Shift voltage arrows/+− outward from path
\ctikzset{voltage/bump b=2.5}              % Curve height of European voltage arrow (default 1.5)
\ctikzset{voltage/distance from node=0.25} % Relative fraction (0..1) or absolute (e.g. 0.25cm)
\ctikzset{voltage/american label distance=0.5} % Distance from +− baseline to text label (default 1.4)
\ctikzset{voltage/american font=\tiny\boldmath} % Font for American + and - signs
\ctikzset{voltage/american plus=\textcolor{red}{$+$}}
\ctikzset{voltage/american minus=\textcolor{blue}{$\vphantom{+}-$}}
```
- `voltage shift=<num>`: moves arrows/+− signs away from the component. Negative values shift to opposite side.
- `voltage shift sources adjust=<num>` (default `0.5`): corrects displacement disparity between sources and passives when using `voltage shift` with scaled components.
- `open voltage position=center|legacy`: `center` (default since v1.1.2) places voltage centered inside open bipole gap; `legacy` restores pre-1.1.2 offset.
- Components with predefined `distance from node` / `bump b` overrides (generic family, `memristor`, `tline`, thermistors, triacs, varistors, `ccapacitor`) ignore global defaults.

## Generators and active source behavior

Active components have internal flags enabling power-delivery conventions.
```latex
% Correct: disable active voltage status BEFORE specifying voltage
\draw (0,0) to[sV, bipole/is voltage=false, v=$V_s$] ++(2,0);

% WRONG: sV=Vs expands to v=Vs before bipole/is voltage=false is parsed!
% \draw (0,0) to[sV=$V_s$, bipole/is voltage=false] ++(2,0);
```
- `bipole/is voltage=false`: forces voltage source to adopt passive voltage/current directions.
- `bipoles/is current=false`: forces current source to adopt passive directions.
- **ORDERING TRAP**: `bipole/is voltage=false` MUST precede `v=...` or `V=...` in the `to[...]` options. If placed after, half the positioning runs in active mode and half in passive mode.
- Global switch: `\ctikzset{bipole/override source vif=true}` (v1.4.4+, experimental) makes all sources passive by default.
  - Stronger than `is voltage`. To locally re-enable active mode for a single component, you MUST pass both: `to[V, bipole/override source vif=false, bipole/is voltage=true, v=va]`.
- Style order trap: `[american, straight voltages]` reverts generator symbol shapes to European circles. Use `[straight voltages, american]` if American generator shapes are needed.

## Accessing label nodes and advanced anchors

### Named text nodes
When a bipole has `name=<id>`, CircuiTikZ creates addressable text nodes:
- `<id>label` (Main label `l`, NO space between ID and label)
- `<id>annotation` (Annotation `a`)
- `<id>voltage` (Voltage label `v`)
- `<id>current` (Current label `i`)
- `<id>flow` (Flow label `f`)
Nodes exist only if the corresponding decoration key was specified on the bipole.

### Introspection macros
- `\ctikzgetdirection{<nodename>}`: returns direction angle in degrees (e.g. `\ctikzgetdirection{R1label}`).
- `\ctikzgetdirection{<id>-Iarrow}` / `\ctikzgetdirection{<id>-Farrow}`: returns current/flow arrow rotation angle.
- `\ctikzgetanchor{<id>}{<type>}`: returns anchor name used to position node; `<type>` is `label`, `annotation`, `Vlab`, `Ilab`, or `Flab`.
- *Leak trap*: `\ctikzgetanchor` is global; if an anchor was not activated in the current bipole, it retains data from previous drawings. Pass dummy `l=~` or bare `v` to activate safely.

### Advanced anchors (v1.2.1+)
Activated on `to[..., name=<id>, v, i, f]` (bare or valued):
- Voltage: `<id>-Vfrom` (start point / + sign), `<id>-Vto` (end point / − sign), `<id>-Vcont1` / `<id>-Vcont2` (Bezier control points for European curve, or midpoint for straight/American), `<id>-Vlab` (label coordinate).
- Current: `<id>-Ifrom` / `<id>-Ito` (wire segment span), `<id>-Ipos` (center coordinate for `currarrow`).
- Flow: `<id>-Ffrom` / `<id>-Fto` (flow span), `<id>-Fpos` (center coordinate for `flowarrow`).

### Suppressing default symbols for custom drawings
```latex
\ctikzset{!vi/.style={no v symbols, no i symbols}}
\ctikzset{bipole voltage style/.style={color=blue}, bipole current style/.style={color=red}}
\draw (0,0) to[R, name=R1, v^=$v_R$, i=$i_R$, !vi] ++(3,0);

% Draw custom colored arrows on the exact native anchors:
\node [currarrow, color=red, rotate=\ctikzgetdirection{R1-Iarrow}] at (R1-Ipos) {};
\draw [color=blue, -Stealth] (R1-Vfrom) .. controls (R1-Vcont1) and (R1-Vcont2) .. (R1-Vto);
```
- `no v symbols`, `no i symbols`, `no f symbols`: keeps label and anchor calculations while hiding default arrowheads / +− marks. Re-enable with `v symbols`, `i symbols`, `f symbols`.
- `v label=<text>`, `i label=<text>`, `f label=<text>`: modifies label string without triggering direction recomputation.

### Text ornament styles
- Keys: `bipole label style`, `bipole annotation style`, `bipole voltage style`, `bipole current style`, `bipole flow style` (and `... append style`).
- **TRAP**: In `to[...]`, style overrides (e.g. `bipole annotation style={color=red}`) MUST be placed at the very START of the `to[...]` argument list, before the component shape, to take effect.

### Automatic VIF queue (v1.8.5+, experimental)
Allows deferring custom voltage/current drawing routines to the end of the picture automatically:
```latex
\newcommand{\placesigns}[4]{% #1=node prefix, #2=arg2, #3=arg3, #4=arg4
    \path ($(#1voltage.center)!0.5cm!(#1-Vto)$) node[#2]{$+$};
    \path ($(#1voltage.center)!0.5cm!(#1-Vfrom)$) node[#2]{$-$};
}
\ctikzset{my v/.style={voltage=raised, no v symbols, vif queue add={placesigns}{#1}{}{}}}

\draw (0,0) to[R=R1, name=vR, v={$u_R$}, my v={red}] ++(3,0);
```
- `vif queue add={<macro>}{<arg2>}{<arg3>}{<arg4>}`: queues macro call `\macro{nodename}{arg2}{arg3}{arg4}`.
- Replayed at environment exit. On older LaTeX or ConTeXt, add `use auto vif` to `tikzpicture` options.
- Manual execution/flush: `\ctikzprocessvif`.
- Custom direction generators: `\ctikzactivatevoltagedirections{<stylename>}`, `\ctikzactivatecurrentdirections`, `\ctikzactivateflowdirections`.
- **TRAP**: `vif queue add` MUST appear last in `to[...]` options, and requires active `v`, `i`, or `f`.

## Bipole terminal poles & transparent poles (ch. 6)

### Pole node usage & shortcuts
- Explicit syntax: `to[R, bipole nodes={none}{osquarepole, fill=red}]`.
- Custom shortcut definition: `\ctikzset{-s/.style={bipole nodes={none}{osquarepole, fill=red}}}` then `to[R, -s]`.
- Drawing order: Bipole nodes are placed *after* the wire path is stroked within the same `\draw` command. Open poles (`ocirc`, `odiamondpole`, `osquarepole`) are filled with `white` (or `open poles fill`) to mask the underlying wire.

### Transparent poles
- Opacity key: `\ctikzset{poles/open fill opacity=<val>}` or style `open poles opacity=<val>`.
- Fill color: `\ctikzset{open poles fill=<color>}` (default `white`).
- **TRAP**: When open pole fill opacity $< 1.0$, the wire passing under the pole becomes visible through the semi-transparent circle. To avoid artifacts, break the path and connect wires to the border anchors of the terminal: `(P.0)`, `(P.180)`, `node[ocirc, anchor=180]{}`.

## Mirroring and inverting
- `to[pD, mirror]`: vertical reflection across the bipole path axis.
- `to[pD, invert]`: horizontal reflection (swaps start and end terminals).
- Combined: `to[pD, mirror, invert]`.
- **TRAP**: `mirror` and `invert` do NOT flip or relocate decoration labels or voltages. Labels remain by default above/right, and voltages below/left.

## FAQ & common failure modes (ch. 8)

### 1. Wire gaps when naming connection points (§8.1)
- **Problem**: Gaps appear in wires when using `node (A) at (x,y) {}` as a junction point.
- **Cause**: TikZ nodes have a default non-zero `inner sep`. Wires terminate at the node's rectangular border rather than its dimensionless center point.
- **RULE**: NEVER use `node` to define an invisible wiring junction. ALWAYS use `\coordinate`:
  ```latex
  % WRONG: Leaves visible wire gaps
  % \path (1,1) node (A){}; \draw (1,0) -- (A) -- (2,1);

  % CORRECT: Zero-size dimensionless anchor
  \path (1,1) coordinate (A); \draw (1,0) -- (A) -- (2,1);
  % OR inline:
  \draw (0,0) to[R] ++(2,0) coordinate(A) -- ++(1,0);
  ```

### 2. Dashed or colored wire segments (§8.2)
- **Problem**: `to[R] to[short, dashed, red] to[R]` colors or dashes the entire path.
- **Cause**: In TikZ, path styling attributes (`draw`, `color`, `dashed`) apply globally to the entire `\draw` statement.
- **Solution A (Recommended — Separate Paths)**:
  ```latex
  \draw (0,0) to[R] ++(2,0) coordinate(a);
  \draw [dashed, red] (a) -- ++(1,0) coordinate(b);
  \draw (b) to[R] ++(2,0);
  ```
- **Solution B (`edge` operation)**:
  ```latex
  \draw (0,0) to[R] ++(2,0) coordinate(a)
        edge[dashed, red] ++(1,0)
        ++(1,0) to[R] ++(2,0);
  \node [ocirc] at (a){};                  % Nodes at edge junctions must be placed manually!
  ```
  *Trap with `edge`*: `edge` creates a subpath that does not advance the parent path coordinate; manual `++(x,y)` coordinate advance is required. Furthermore, `edge` is drawn after nodes, so terminal poles at the junction must be placed manually.

### 3. Emergency stop with `\tikzexternalize` (§8.3)
- **Problem**: LaTeX throws `! Emergency stop` during picture externalization.
- **Cause**: TikZ externalization parser searches strictly for `\end{tikzpicture}`.
- **FIX**: Replace every `\begin{circuitikz}` ... `\end{circuitikz}` with `\begin{tikzpicture}` ... `\end{tikzpicture}`. They share identical options and internals.

### 4. Global scaling and axis flipping (§8.5)
- **Problem**: `yscale=-1` or unequal `xscale`/`yscale` produces inverted text, distorted arcs, and broken component geometry.
- **Rule**: NEVER use negative axis scaling (`yscale=-1`) or anisotropic coordinate scaling with CircuiTikZ. Flip circuits logically using `mirror`, `invert`, or coordinate math.
- `pic` compatibility: Fixed in v1.8.0. In older versions, bipoles inside TikZ `pic` constructs render with incorrect rotations.

### 5. Tunable component arrow direction (§8.6)
- Since v1.3.3, arrows on variable elements (`vR`, `variable capacitor`, `tgeneric`, `variable cute inductor`) point consistently from bottom-left to top-right.
- Revert to legacy direction: `\ctikzset{bipoles/fix tunable direction=false}`.
- Tip customization: `\ctikzset{tunable end arrow={Bar}, tunable start arrow={Stealth}}`.

## Family keys (\ctikzset)
`voltage dir=<RP|EF|old|noold>` → Sets global voltage and current direction policy.
`RPvoltages` → Style alias for `voltage dir=RP` (rising potential standard, recommended).
`EFvoltages` → Style alias for `voltage dir=EF` (electric field direction standard).
`oldvoltagedirection` → Style alias for `voltage dir=old` (pre-v0.5 legacy direction).
`nooldvoltagedirection` → Style alias for `voltage dir=noold` (v0.5+ uncorrected field direction).
`voltage=<european|straight|american|raised>` → Sets voltage graphic style.
`europeanvoltages` → Style alias for `voltage=european` (curved arrows).
`straightvoltages` → Style alias for `voltage=straight` (straight arrows).
`americanvoltages` → Style alias for `voltage=american` (+/− signs on wire).
`raised voltages` → Style alias for `voltage=raised` (+/− signs raised to label baseline).
`voltage shift=<number>` → Shifts voltage arrows or signs away from bipole center.
`voltage shift sources adjust=<number>` → Compensation factor (default 0.5) for scaled sources under voltage shift.
`voltage/shift=<dimension>` → Fine-tune offset of +/− symbols independently of label.
`voltage/american label distance=<number>` → Distance of voltage label from +/− sign line (default 1.4).
`voltage/american font=<fontcmd>` → Font applied to American + and − symbols.
`voltage/american plus=<macro>` → Code used for American plus sign (default `$+$`).
`voltage/american minus=<macro>` → Code used for American minus sign (default `$\vphantom{+}-$`).
`voltage/distance from node=<number|dimen>` → Start/stop margin for voltage arrow from terminal node (default 0.5).
`voltage/bump b=<number>` → Curvature height parameter for European curved arrows (default 1.5).
`voltage/european label distance=<number>` → Distance from European curved arrow to label (default 1.4).
`open voltage position=<center|legacy>` → Positions open-circuit voltage at center of gap (default) or legacy offset.
`current/distance=<number>` → Position fraction along wire for inline current arrow (default 0.5).
`flow/distance=<number>` → Position fraction along component for flow arrow (default 0.5).
`label distance=<dimension>` → Distance adjustment for main label `l` (positive outward, negative inward).
`annotation distance=<dimension>` → Distance adjustment for annotation `a`.
`label/align=<smart|rotate|straight>` → Orientation alignment mode for bipole labels.
`smartlabels` → Package option / style rotating labels except near vertical multiples.
`rotatelabels` → Package option / style rotating all labels parallel to bipole path.
`straightlabels` → Package option / style keeping all labels strictly horizontal.
`l2 halign=<l|c|r>` → Horizontal text alignment in stacked label `l2` (default `l`).
`l2 valign=<t|c|b>` → Vertical text alignment in stacked label `l2` (default `c`).
`a2 halign=<l|c|r>` → Horizontal text alignment in stacked annotation `a2` (default `l`).
`a2 valign=<t|c|b>` → Vertical text alignment in stacked annotation `a2` (default `c`).
`bipole/is voltage=<true|false>` → Flag treating component as voltage source (`false` reverts to passive).
`bipoles/is current=<true|false>` → Flag treating component as current source (`false` reverts to passive).
`bipole/override source vif=<true|false>` → Experimental switch enforcing passive positioning on all sources.
`no v symbols` → Suppresses voltage arrows/signs while keeping text label and anchors active.
`no i symbols` → Suppresses current arrows while keeping current text label and anchors active.
`no f symbols` → Suppresses flow arrows while keeping flow text label and anchors active.
`v symbols` → Re-enables voltage arrow / sign rendering.
`i symbols` → Re-enables current arrow rendering.
`f symbols` → Re-enables flow arrow rendering.
`v label=<text>` → Overrides voltage label string without changing symbol direction or activating anchors.
`i label=<text>` → Overrides current label string without changing symbol direction.
`f label=<text>` → Overrides flow label string without changing symbol direction.
`bipole label style/.style={<keys>}` → TikZ style applied to the `namelabel` text node.
`bipole annotation style/.style={<keys>}` → TikZ style applied to the `nameannotation` text node.
`bipole voltage style/.style={<keys>}` → TikZ style applied to the `namevoltage` text node.
`bipole current style/.style={<keys>}` → TikZ style applied to the `namecurrent` text node.
`bipole flow style/.style={<keys>}` → TikZ style applied to the `nameflow` text node.
`bipole nodes={<start>}{<stop>}` → Places pole shapes at start and end terminals of a bipole path.
`open poles fill=<color>` → Background fill color for open poles (`ocirc`, `osquarepole`, `odiamondpole`; default white).
`poles/open fill opacity=<number>` → Fill opacity for open poles (overrides global draw opacity).
`open poles opacity=<number>` → Style alias for `poles/open fill opacity`.
`bipoles/fix tunable direction=<true|false>` → Enforces bottom-left to top-right arrows on tunable bipoles (default true).
`tunable end arrow={<tip>}` → Sets TikZ arrow tip for tunable component pointers.
`tunable start arrow={<tip>}` → Sets start arrow tip for tunable component pointers.
`vif queue add={<macro>}{<arg2>}{<arg3>}{<arg4>}` → Appends custom rendering macro to automatic vif queue (experimental).
`use auto vif` → Enables automatic end-of-environment execution of vif queue on older LaTeX engines.

## Traps
1. **Active vs passive shortcut collision**: `to[R=10\Omega]` sets label `l`, but `to[V=10V]` sets voltage `v`, and `to[I=1A]` sets current `i`. Silent semantics change.
2. **Parser crash on unbraced `=` or `,`**: Writing `to[R, l=$R=3$]` or `to[ospst=open, 3s]` breaks TikZ option parsing. Enclose the whole argument in braces: `l={$R=3$}`, `ospst={open, 3s}`.
3. **Ordering trap on active source de-powering**: In `to[...]`, `bipole/is voltage=false` MUST precede `v=...` (or shorthand `sV=...`). If placed after, half the positioning logic runs in active mode and half in passive mode.
4. **Stacked labels with `siunitx` crash**: Writing `l2_=$R_1$ and 1<\kilo\ohm>` throws an error. The `<...>` shorthand is forbidden in `l2`/`a2`; use explicit `\SI{1}{\kilo\ohm}` or `\qty{1}{\kilo\ohm}`.
5. **Stacked labels TeX engine limit**: `l2` and `a2` rely on LaTeX `tabular` and fail catastrophically under Plain TeX or ConTeXt.
6. **Style option order changes source shapes**: `[american, straight voltages]` reverts voltage generator symbols to European circles. Use `[straight voltages, american]` to keep American generator shapes.
7. **Ornament style placement**: Keys like `bipole annotation style={color=red}` must appear at the VERY START of the `to[...]` argument list, before the bipole name, or they are ignored.
8. **Arrow style immutability**: Voltage and current arrows are part of the bipole's internal path; standard TikZ path options (`draw=red, -Stealth`) will not restyle arrows. Use `no v symbols` + advanced anchors to draw custom arrows.
9. **Anchor leakage across figures**: `\ctikzgetanchor` is global; calling it without activating the corresponding anchor in the current bipole returns stale data from prior drawings. Declare dummy `l=~` or bare `v` first.
10. **VIF queue position**: `vif queue add` must be the last argument in `to[...]`, and only functions on CircuiTikZ bipoles with active anchors, never on generic TikZ paths.
11. **Wire gaps from `node` instead of `coordinate`**: TikZ nodes have positive default `inner sep`. Always use `\coordinate` to name junction points.
12. **Dashed/colored wires fail in single path**: TikZ path options apply globally to the whole `\draw` statement. Split the path into separate `\draw` commands connected at shared coordinates.
13. **Transparent pole mask failure**: Setting `open poles opacity` $< 1.0$ reveals the underlying wire passing through the pole center. Connect wires manually to border anchors (`(P.0)`, `(P.180)`).
14. **Mirror/invert ignores decorations**: `mirror` and `invert` flip the bipole symbol but do NOT swap or flip label and voltage placements.
15. **Global negative scaling breaks layout**: `yscale=-1` or anisotropic scaling corrupts arc calculations, label anchors, and text orientation.
16. **Predefined bump/distance overrides**: Generic components (`generic`, `ageneric`, `memristor`, `tline`, `varistor`, `ccapacitor`) define built-in overrides for `distance from node` or `bump b`, ignoring global `\ctikzset` defaults.

Cross-checked against manual index: 51 names verified.
