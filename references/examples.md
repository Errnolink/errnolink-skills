# Worked example circuits (manual tutorials + ch. 10)

> Read this when: starting a new circuit and wanting a proven skeleton, or reusing a block. Each example teaches a pattern — steal the pattern, not just the code.

## 1. Current shunt with KCL labels (tutorial §2.1)

Teaches: meshes as paths, `i>`/`i_` placement for readability, `-*` junction dots, `voltage shift` to breathe.

```latex
\begin{circuitikz}[american, voltage shift=0.5]
\draw (0,0) to[isource, l=$I_0$, v=$V_0$] (0,3)
      to[short, -*, i=$I_0$] (2,3)
      to[R=$R_1$, i>_=$i_1$] (2,0) -- (0,0);
\draw (2,3) -- (4,3) to[R=$R_2$, i>_=$i_2$] (4,0)
      to[short, -*] (2,0);
\end{circuitikz}
```

## 2. Non-inverting op-amp amplifier + reusable macro (tutorial §2.2)

Teaches: node-style op amp with `anchor=+`, `noinv input up`, feedback via `coordinate(FB)` and `(FB -| OA.out)`, poles at the end, macro parameterization.

```latex
\ctikzset{amplifiers/fill=cyan!20, component text=left}
\newcommand\myNIA[4]{% #1:name #2:start coord #3:R1 #4:R2
\draw #2 coordinate(#1-in) to[short] ++(1,0)
  node[op amp, noinv input up, anchor=+](#1-OA){\texttt{#1}}
  (#1-OA.-) -- ++(0,-1) coordinate(#1-FB)
  to[R=#3] ++(0,-2) node[ground]{}
  (#1-FB) to[R=#4, *-] (#1-FB -| #1-OA.out) -- (#1-OA.out)
  to[short, *-] ++(1,0) coordinate(#1-out);
}
```
% Call INSIDE a `circuitikz` environment (`\draw` only exists there):
```latex
\begin{circuitikz}[scale=0.7, transform shape]
\myNIA{OA1}{(0,0)}{$R_1$}{$R_2$}
\node[ocirc] at (OA1-in) {};
\end{circuitikz}
```
Draw the open poles *after* the macro to avoid artifacts.

## 3. Two-stage BJT/MOSFET amplifier skeleton (tutorial §2.3 pattern)

Teaches: named supply rails so rails align, relative coordinates everywhere, `-|` for ground alignment.

```latex
\begin{circuitikz}[american]
\draw (0,0) node[nmos](Q1){Q1};
\draw (Q1.S) to[R, l2^=$R_S$ and \SI{5}{k\ohm}] ++(0,-3)
  node[vee](VEE){$V_{EE}=\SI{-10}{V}$};
\draw (Q1.D) to[R, l2_=$R_D$ and \SI{10}{k\ohm}] ++(0,3)
  node[vcc](VCC){$V_{CC}=\SI{10}{V}$};
\draw (Q1.S) to[short] ++(2,0) to[C=$C_1$] ++(0,-1.5) node[ground](GND){};
\end{circuitikz}
```

## 4. Meshed RLC with diode and controlled source (§10.1)

Teaches: multiple meshes in one `\draw`, siunitx compact labels, mixing node-only decorations at the end.

```latex
\begin{circuitikz}[scale=1.4]\draw
(0,0) to[C, l=10<\micro\farad>] (0,2) -- (0,3)
to[R, l=2.2<\kilo\ohm>] (4,3) -- (4,2)
to[L, l=12<\milli\henry>, i=$i_1$, v=b] (4,0) -- (0,0)
(4,2) to[D*, color=red] (2,0)
(0,2) to[R, l=1<\kilo\ohm>, *-] (2,2)
to[cV, i=1, -*, v=$\SI{.3}{\kilo\ohm}\,i_1$] (4,2)
(2,0) to[I, i=1<\milli\ampere>, *-*] (2,2);
\end{circuitikz}
```

## 5. Sallen-Key low-pass cell (§10.4)

Teaches: op amp + inline caps to ground, feedback tee via `|-`.

```latex
\begin{circuitikz}[scale=1]\draw
(5,.5) node [op amp] (opamp) {}
(0,0) node [left] {$U_{we}$} to [R, l=$R_d$, o-*] (2,0)
to [R, l=$R_d$, *-*] (opamp.+)
to [C, l_=$C_{d2}$, *-] ($(opamp.+)+(0,-2)$) node [ground] {}
(opamp.out) |- (3.5,2) to [C, l_=$C_{d1}$, *-] (2,2) to [short] (2,0)
(opamp.-) -| (3.5,2)
(opamp.out) to [short, *-o] (7,.5) node [right] {$U_{wy}$};
\end{circuitikz}
```

## 6. RF mixer block diagram (§10.6, abridged)

Teaches: node-style blocks (wilkinson/coupler) with bezier interconnects, per-component geometry keys.

```latex
\ctikzset{quadpoles/coupler/width=1}\ctikzset{tripoles/wilkinson/width=1}
\draw
(-2,0) node[wilkinson](w1){}
(2,0) node[coupler] (c1) {}
(0,2) node[coupler,rotate=90] (c2) {}
(w1.out1) .. controls ++(0.8,0) and ++(0,0.8) .. (c2.port4)
(w1.in) to[short,-o] ++(-1,0) (w1.in) node[left=30] {LO}
(c1.port3) to[short,-o] ++(1,0) (c1.port3) node[right=30] {RF};
```

## 7. Three-phase HVDC block schematic (§10.9, abridged)

Teaches: local style shorthands, converter block anchors (`ac up in`, `dc up out`), `oosourcetrans` with `prim=/sec=` three-phase symbols, `tmultiwire` buses, `gridnode`.

```latex
\begin{circuitikz}[smallR/.style={european resistor, resistors/scale=0.5}]
\draw (0,0) node[tacdcshape, anchor=ac mid in](acdc){} to[smallR] ++(-2,0)
  -- coordinate(point) node[circ](){} ++(-.5,0);
\draw (acdc.ac up in) to[nos, invert, mirror, name=switch, color=red] ++(-2,0) -- (point);
\draw (acdc.ac down in) to[smallR] ++(-2,0) -- (point)
  to[oosourcetrans, prim=wye, sec=delta, l=transformer] ++(-1.5,0)
  to[tmultiwire] ++(-.5,0) node[gridnode, anchor=right]{};
\end{circuitikz}
```

## 8. Logic circuit with SR latch (tutorial §2.4 pattern)

Teaches: gate placement by anchor, bus-style routing with `-|`, dot junctions.

```latex
\begin{circuitikz}
\draw (0,2) node[and port](A1){} (0,0) node[nor port](N1){} (2,1) node[nor port](N2){};
\draw (A1.out) -- ++(0.5,0) coordinate(t1) |- (N2.in 1);
\draw (N1.out) -- ++(0.5,0) coordinate(t2) |- (N2.in 2);
\draw (N2.out) to[short, -*] ++(1,0) coordinate(q) |- (A1.in 2);
\end{circuitikz}
```

## Patterns to remember

- First coordinate absolute, rest relative (`++`) → copy-paste-safe circuits.
- Feedback tees: `coordinate(FB)` at the tap, then `(FB -| node.out)` for the crossing corner.
- Decorations (`i>_=`, `v^>=`, poles) compose on any `to[]`, including `to[short, ...]` for plain wires.
- Put "special" symbols (`node[circ]{}` extra dots, ocirc terminals drawn later) at the end of the path to avoid pole-order artifacts.

Cross-checked against manual index: 45+ names verified.
