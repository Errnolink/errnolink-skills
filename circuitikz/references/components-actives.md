# Actives, Blocks, Tubes, and Switches (§4.14–§4.21, pp. 110–187)
> Read this when drawing: block diagrams, RF systems, transistors (BJT, FET, MOSFET, IGBT, JFET, UJT, GFET), vacuum tubes, electro-mechanical devices, transformers and double bipoles, operational and instrumentation amplifiers, switches, buttons, and jumpers.

## Syntax recap
Verbatim canonical example from manual (§4.15.10, p. 150):
```latex
\begin{circuitikz} \draw[yscale=1.1, xscale=.8]
(2,4.5) -- (0,4.5) to[Tpmos=p1, n=p1] (0,3)
to[Tnmos=n1, n=n1] (0,1.5)
to[Tnmos=n2, n=n2] (0,0) node[ground] {}
(2,4.5) to[Tpmos=p2,n=p2] (2,3) to[short, -*] (0,3)
(p1.G) -- (n1.G) to[short, *-o] ($(n1.G)+(3,0)$)
(n2.G) ++(2,0) node[circ] {} -| (p2.G)
(n2.G) to[short, -o] ($(n2.G)+(3,0)$)
(0,3) to[short, -o] (-1,3)
;\end{circuitikz}
```
Orientation:
- Two placement styles: multi-terminal devices (transistors, op-amps, tubes, transformers, rotary switches) are instantiated via `\node[<shape>](<id>){<text>};`. Bipole blocks, traditional switches, and jumpers use path syntax `to[<shape>=<label>, n=<id>]`.
- Path-style transistors use prefix `T` (e.g. `to[Tnmos=n1, n=n1]`, `to[Tpmos=p1, mirror]`). Use `n=<id>` or `name=<id>` to expose gate/base terminals (`(n1.G)`).
- Terminal anchors: BJTs expose `B`/`base`, `C`/`collector`, `E`/`emitter`; FETs expose `G`/`gate`, `D`/`drain`, `S`/`source`, `bulk`; op-amps expose `+`, `-`, `out`, `up`, `down`; transformers expose `A1`, `A2`, `B1`, `B2`.
- Flipping and orientation: on node components use `xscale=-1` or `yscale=-1`, counteracting mirrored text via `\ctikzflipx{<label>}` or `\scalebox{-1}[1]{<label>}`. On path bipoles use `mirror` (flip side of wire) and `invert` (swap port sequence).
- Connection poles: attach terminal dots using path pole shorthands `-*`, `*-*`, `-o`, `o-o`.

## Component inventory

State fill-variant shorthands (`*`=full, `-`=stroke, `o`=empty) apply to connection poles; components marked fillable honor TikZ `fill=<color>` or specific class fill keys.

### 1. Block Diagram Components (§4.14, pp. 110–125)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `mixer` | — | `node[mixer]` | `blocks` | `w`, `s`, `e`, `n`, `center`, `geocenter`, `left up/down`, `right up/down` | Round mixer; options `box`/`boxed`, `box only`/`boxed only`; fillable |
| `adder` | — | `node[adder]` | `blocks` | `w`, `s`, `e`, `n`, `center`, `geocenter`, `left up/down`, `right up/down` | Round adder with internal cross; `geocenter` is geometric circle center; fillable |
| `oscillator` | — | `node[oscillator]` | `blocks` | `w`, `s`, `e`, `n`, `center`, `geocenter`, `left up/down`, `right up/down` | Round oscillator; `center` is displaced leftward for layout convenience; fillable |
| `circulator` | — | `node[circulator]` | `blocks` | `w`, `s`, `e`, `n`, `center`, `geocenter`, `left/right up/down` | Directional circulator; accepts `box`/`boxed`; fillable |
| `wilkinson` | — | `node[wilkinson]` | `blocks` | `in`, `out1`, `out2` | Wilkinson power divider; node text printed outside bounding box; fillable |
| `splitter` | — | `node[splitter]` | `blocks` | `in`, `out1`, `out2` | Resistive power splitter; fillable |
| `genericsplitter` | — | `node[genericsplitter]` | `blocks` | `in`, `out1`, `out2` | Generic splitter with text label (e.g. `\SI{-3}{dB}`); fillable |
| `mzm` | — | `node[mzm]` | `blocks` | `in`, `out`, `mod` | Mach-Zehnder optical modulator; fillable |
| `fourport` | — | `node[fourport]` | `blocks` | `port1`..`port4`, `left up/down`, `right up/down` | Generic 4-port box; fillable |
| `coupler` | — | `node[coupler]` | `blocks` | `port1`..`port4`, `left up/down`, `right up/down` | Directional coupler with straight coupling arrows; fillable |
| `coupler2` | — | `node[coupler2]` | `blocks` | `port1`..`port4`, `left up/down`, `right up/down` | Directional coupler with curved coupling arrows; fillable |
| `gridnode` | — | `node[gridnode]` | `blocks` | `left`, `right`, `up`, `down`, `center` | Grid-filled block; line count set via `gridlines=N` (default 7); fillable |
| `twoport` | — | `to[twoport]` (`twoportshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Generic two-port; `t=`/`uset=` sets inner label, `>` adds input arrow; fillable |
| `twoportsplit` | — | `to[twoportsplit]` (`twoportsplitshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Diagonally split two-port; `t1=` and `t2=` set triangle labels; fillable |
| `vco` | — | `to[vco]` (`vcoshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Voltage controlled oscillator; accepts `box` option; fillable |
| `bandpass` | — | `to[bandpass]` (`bandpassshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Bandpass filter with wave diagram; fillable |
| `bandstop` | — | `to[bandstop]` (`bandstopshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Bandstop / notch filter with wave diagram; fillable |
| `highpass` | — | `to[highpass]` (`highpassshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Highpass filter (3 waves); `highpass2` = simplified (2 waves); fillable |
| `lowpass` | — | `to[lowpass]` (`lowpassshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Lowpass filter (3 waves); `lowpass2` = simplified (2 waves); fillable |
| `highpass2` | — | `to[highpass2]` (`highpass2shape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Simplified highpass filter with 2 waves; fillable |
| `lowpass2` | — | `to[lowpass2]` (`lowpass2shape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Simplified lowpass filter with 2 waves; fillable |
| `allpass` | — | `to[allpass]` (`allpassshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Allpass filter block; fillable |
| `vallpass` | — | `to[vallpass]` (`vallpassshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Variable allpass filter block; fillable |
| `lowpassp` | — | `to[lowpassp]` (`lowpasspshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Lowpass Bode-plot style; options `ideal filter`, `ideal filter numbers`; fillable |
| `highpassp` | — | `to[highpassp]` (`highpasspshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Highpass Bode-plot style; fillable |
| `bandpassp` | — | `to[bandpassp]` (`bandpasspshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Bandpass Bode-plot style; fillable |
| `bandstopp` | — | `to[bandstopp]` (`bandstoppshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Bandstop Bode-plot style; fillable |
| `resonantp` | — | `to[resonantp]` (`resonantpshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Resonant peak Bode-plot style; fillable |
| `notchp` | — | `to[notchp]` (`notchpshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Notch Bode-plot style; fillable |
| `bgenerator` | — | `to[bgenerator]` (`bgeneratorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Block generator; `t=` or `uset=` sets label; fillable |
| `qgenerator` | — | `to[qgenerator]` (`qgeneratorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Crystal generator; `t=` sets label; fillable |
| `cgenerator` | — | `to[cgenerator]` (`cgeneratorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Clock generator; fillable |
| `ngenerator` | — | `to[ngenerator]` (`ngeneratorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Generic generator; `t=` sets label; fillable |
| `adc` | — | `to[adc]` (`adcshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Analog-to-digital converter ($A \to D$); fillable |
| `dac` | — | `to[dac]` (`dacshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Digital-to-analog converter ($D \to A$); fillable |
| `dsp` | — | `to[dsp]` (`dspshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Digital signal processor block; fillable |
| `fft` | — | `to[fft]` (`fftshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Fast Fourier transform block; fillable |
| `amp` | — | `to[amp]` (`ampshape`) | `blocks` | `left`, `right`, `up`, `down`, `left/right up/down` | Block amplifier; accepts `box`/`boxed`, `t=` label; fillable |
| `iamp` | — | `to[iamp]` (`iampshape`) | `blocks` | `left`, `right`, `up`, `down`, `left/right up/down` | Instrumentation amplifier block; accepts `box`/`boxed`; fillable |
| `vamp` | — | `to[vamp]` (`vampshape`) | `blocks` | `left`, `right`, `up`, `down`, `left/right up/down` | Variable gain amplifier (VGA); accepts `box`/`boxed`; fillable |
| `piattenuator` | — | `to[piattenuator]` (`piattenuatorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | $\pi$-type fixed attenuator; fillable |
| `vpiattenuator` | — | `to[vpiattenuator]` (`vpiattenuatorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Variable $\pi$-type attenuator; fillable |
| `tattenuator` | — | `to[tattenuator]` (`tattenuatorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | T-type fixed attenuator; fillable |
| `vtattenuator` | — | `to[vtattenuator]` (`vtattenuatorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Variable T-type attenuator; fillable |
| `phaseshifter` | — | `to[phaseshifter]` (`phaseshiftershape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Phase shifter ($\\phi$); fillable |
| `vphaseshifter` | — | `to[vphaseshifter]` (`vphaseshiftershape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Variable phase shifter; fillable |
| `biast` | — | `to[biast]` (`biastshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Bias-T injection network; fillable |
| `sinetable` | — | `to[sinetable]` (`sinetableshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Sine lookup table; fillable |
| `register` | — | `to[register]` (`registershape`) | `blocks` | `left`, `right`, `clk`, `left/right up/down` | Digital register with clock input; fillable |
| `detector` | — | `to[detector]` (`detectorshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | RF / envelope detector; fillable |
| `saturation` | — | `to[saturation]` (`saturationshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Saturation nonlinearity block; fillable |
| `sigmoid` | — | `to[sigmoid]` (`sigmoidshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Sigmoid activation function block; fillable |
| `allornothing` | — | `to[allornothing]` (`allornothingshape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Threshold / comparator block; fillable |
| `fiber` | — | `to[fiber]` (`fibershape`) | `blocks` | `left`, `right`, `left up/down`, `right up/down` | Optical fiber transmission line; fillable |
| `sdcdc` | — | `to[sdcdc]` (`sdcdcshape`) | `blocks` | `dc up in/out`, `dc down in/out`, `left/right up/down` | Single-wire DC/DC converter; dashed DC line via `blocks dc segments`; fillable |
| `sacdc` | — | `to[sacdc]` (`sacdcshape`) | `blocks` | `ac up/down in`, `dc up/down out`, `left/right up/down` | 1-phase AC/DC converter (rectifier); fillable |
| `sdcac` | — | `to[sdcac]` (`sdcacshape`) | `blocks` | `dc up/down in`, `ac up/down out`, `left/right up/down` | 1-phase DC/AC converter (inverter); fillable |
| `sacac` | — | `to[sacac]` (`sacacshape`) | `blocks` | `ac up/down in`, `ac up/down out`, `left/right up/down` | 1-phase AC/AC converter; fillable |
| `tacdc` | — | `to[tacdc]` (`tacdcshape`) | `blocks` | `ac up/mid/down in`, `dc up/down out`, `left/right up/down` | 3-phase AC/DC converter; fillable |
| `tdcac` | — | `to[tdcac]` (`tdcacshape`) | `blocks` | `dc up/down in`, `ac up/mid/down out`, `left/right up/down` | 3-phase DC/AC converter; fillable |
| `tacac` | — | `to[tacac]` (`tacacshape`) | `blocks` | `ac up/mid/down in`, `ac up/mid/down out` | 3-phase AC/AC converter; fillable |
| `camera` | — | `to[camera]` (`camerashape`) | `misc` | `left`, `right`, geographic | Video camera block; fillable |
| `tvset` | — | `to[tvset]` (`tvsetshape`) | `blocks` | `left`, `right`, `top`, `bottom` | Television receiver set; fillable |
| `trx` | — | `to[trx]` (`trxshape`) | `blocks` | `east`, `west`, `south`, `plus`, `minus` | Radio transceiver (HAM radio); fillable |
| `swr` | — | `to[swr]` (`swrshape`) | `blocks` | `east`, `west`, `south` | Standing wave ratio (SWR) meter; fillable |
| `power` | — | `to[power]` (`powershape`) | `blocks` | `plus`, `minus`, `u1`, `u2`, `south` | Power supply unit with DC (`plus`/`minus`) and AC (`u1`/`u2`); fillable |

### 2. Transistors (§4.15, pp. 125–151)
Transistor customization flags compose freely: `photo` (light rays), `schottky base` (hooked base), `nobase`/`nogate` (omit terminal line), `bodydiode` (freewheeling diode), `tr circle` (package housing), `solderdot` (internal dot on gate/source), `fdsoi`/`fdsoidot` (back gate / back dot), `bulk` (bulk terminal).
Path syntax: prepend `T` to the transistor name (e.g. `to[Tnpn]`, `to[Tnmos=M1, n=M1]`).
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `npn` | — | `node[npn]` / `to[Tnpn]` | `transistors` | `B`/`base`, `C`/`collector`, `E`/`emitter`, `nobase`, `body C/E in/out`, `circle center/C/E` | Standard NPN BJT; options `photo`, `bodydiode`, `schottky base`, `tr circle` |
| `pnp` | — | `node[pnp]` / `to[Tpnp]` | `transistors` | `B`, `C`, `E`, `nobase`, `body C/E in/out`, `circle center/C/E` | Standard PNP BJT; options `photo`, `bodydiode`, `schottky base`, `tr circle` |
| `bjtnpn` | — | `node[bjtnpn]` | `transistors` | `B`, `cbase`, `C`, `E`, `C1`..`CN`, `E1`..`EN`, `nobase`, `vcenter`, `gcenter` | Multi-terminal NPN; keys `collectors=N`, `emitters=N`; `photo`/`bodydiode` ignored |
| `bjtpnp` | — | `node[bjtpnp]` | `transistors` | `B`, `cbase`, `C`, `E`, `C1`..`CN`, `E1`..`EN`, `nobase`, `vcenter`, `gcenter` | Multi-terminal PNP; keys `collectors=N`, `emitters=N`; `photo`/`bodydiode` ignored |
| `nigbt` | — | `node[nigbt]` | `transistors` | `B`, `C`, `E`, `nobase`, `nobulk`, `centergap` | N-channel IGBT; outer base customizable |
| `pigbt` | — | `node[pigbt]` | `transistors` | `B`, `C`, `E`, `nobase`, `nobulk`, `centergap` | P-channel IGBT; outer base customizable |
| `Lnigbt` | — | `node[Lnigbt]` | `transistors` | `B`, `C`, `E`, `nobase`, `body C/E in/out` | N-channel IGBT with body diode connection anchors |
| `Lpigbt` | — | `node[Lpigbt]` | `transistors` | `B`, `C`, `E`, `nobase`, `body C/E in/out` | P-channel IGBT with body diode connection anchors |
| `nmos` | — | `node[nmos]` / `to[Tnmos]` | `transistors` | `G`/`gate`, `D`/`drain`, `S`/`source`, `bulk`, `nobulk`, `centergap` | N-channel MOSFET; options `bulk`, `fdsoi`, `fdsoidot`, `arrowmos`, `ferroel gate` |
| `pmos` | — | `node[pmos]` / `to[Tpmos]` | `transistors` | `G`, `D`, `S`, `bulk`, `nobulk`, `centergap` | P-channel MOSFET; options `emptycircle`, `nocircle`, `bulk`, `arrowmos` |
| `nmosd` | — | `node[nmosd]` / `to[Tnmosd]` | `transistors` | `G`, `D`, `S`, `bulk`, `nobulk` | Simplified depletion NMOS (thick channel); color via `tripoles/nmosd/depletion color` |
| `pmosd` | — | `node[pmosd]` / `to[Tpmosd]` | `transistors` | `G`, `D`, `S`, `bulk`, `nobulk` | Simplified depletion PMOS (thick channel); color via `tripoles/pmosd/depletion color` |
| `hemt` | — | `node[hemt]` | `transistors` | `G`, `D`, `S`, `nogate`/`nobase` | High-electron-mobility transistor; options `split gate`, `nobase` |
| `GaN hemt` | — | `node[GaN hemt]` | `transistors` | `G`, `D`, `S` | Predefined styled HEMT (`split gate=true`, `source arrow=1`, `base height=0.6`) |
| `nfet` | — | `node[nfet]` | `transistors` | `G`, `D`, `S`, `centergap` | N-channel FET; options `solderdot`/`nosolderdot`, `fdsoi` |
| `pfet` | — | `node[pfet]` | `transistors` | `G`, `D`, `S`, `centergap` | P-channel FET; options `solderdot`/`nosolderdot`, `fdsoi` |
| `nfetd` | — | `node[nfetd]` | `transistors` | `G`, `D`, `S`, `centergap` | Depletion N-channel FET |
| `pfetd` | — | `node[pfetd]` | `transistors` | `G`, `D`, `S`, `centergap` | Depletion P-channel FET |
| `nigfete` | — | `node[nigfete]` | `transistors` | `G`, `D`, `S`, `G2`/`gate2`, `centergap` | Enhancement N-IGFET; options `doublegate`, `solderdot`, `fdsoi` |
| `nigfetebulk` | — | `node[nigfetebulk]` | `transistors` | `G`, `D`, `S`, `bulk`, `G2`, `centergap` | Enhancement N-IGFET with separate bulk connection |
| `nigfetd` | — | `node[nigfetd]` | `transistors` | `G`, `D`, `S`, `G2`, `centergap` | Depletion N-IGFET; options `doublegate`, `solderdot`, `fdsoi` |
| `pigfete` | — | `node[pigfete]` | `transistors` | `G`, `D`, `S`, `G2`, `centergap` | Enhancement P-IGFET; options `doublegate`, `solderdot`, `fdsoi` |
| `pigfetebulk` | — | `node[pigfetebulk]` | `transistors` | `G`, `D`, `S`, `bulk`, `G2`, `centergap` | Enhancement P-IGFET with separate bulk connection |
| `pigfetd` | — | `node[pigfetd]` | `transistors` | `G`, `D`, `S`, `G2`, `centergap` | Depletion P-IGFET; options `doublegate`, `solderdot`, `fdsoi` |
| `njfet` | — | `node[njfet]` / `to[Tnjfet]` | `transistors` | `G`, `D`, `S` | N-channel JFET; gate height adjustable via `transistors/jfet gate height` |
| `pjfet` | — | `node[pjfet]` / `to[Tpjfet]` | `transistors` | `G`, `D`, `S` | P-channel JFET |
| `nujt` | — | `node[nujt]` | `transistors` | `B1`, `B2`, `E`, `G`, `kink` | N-channel unijunction transistor; `nobase` shifts `E` anchor (`G` stays fixed) |
| `pujt` | — | `node[pujt]` | `transistors` | `B1`, `B2`, `E`, `G`, `kink` | P-channel unijunction transistor |
| `isfet` | — | `node[isfet]` | `transistors` | `G`, `D`, `S` | Ion-sensitive FET |
| `ngfet` | — | `node[ngfet]` | `transistors` | `outer hex up/down`, `inner hex up/down`, `right` | N-type graphene FET (hexagonal channel); fillable |
| `pgfet` | — | `node[pgfet]` | `transistors` | `outer hex up/down`, `inner hex up/down`, `right` | P-type graphene FET; fillable |

### 3. Electronic Tubes (§4.16, pp. 151–158)
All tubes belong to class `tubes` and are fillable (`fill=<color>` or `tubes/fill`).
Shorthands: `filament` (heater filament), `nocathode` (omit cathode bar), `fullcathode` (split cathode terminals `cathode 1`/`cathode 2`).
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `diodetube` | — | `node[diodetube]` | `tubes` | `anode`, `cathode`, `filament 1/2`, `filament center`, `cathode 1/2`, `tube top/bottom/left/right` | Vacuum diode; avoids clash with semiconductor diode; options `filament`, `fullcathode` |
| `triode` | — | `node[triode]` | `tubes` | `anode`, `cathode`, `control` (grid), filament anchors | Triode tube; grid dashed line |
| `tetrode` | — | `node[tetrode]` | `tubes` | `anode`, `cathode`, `control`, `screen`, filament anchors | Tetrode tube (control + screen grids) |
| `pentode` | — | `node[pentode]` | `tubes` | `anode`, `cathode`, `control`, `screen`, `suppressor` | Pentode tube with 3 independent grids |
| `pentode suppressor to cathode` | — | `node[pentode suppressor to cathode]` | `tubes` | `anode`, `cathode`, `control`, `screen` | Pentode with suppressor wired internally to cathode |
| `matube` | — | `node[matube]` | `tubes` | `anode 1`..`N` (`nixie k1`..`kN`), `cathode` (`nixie a`), `control` | Multi-anode tube; options `anodes=N`, `anodedot`, `nixieanode`, `nogrid` |
| `magnetron` | — | `node[magnetron]` | `tubes` | `anode`, `cathode1`, `cathode2`, `left`, `right`, `top` | Microwave cavity magnetron |
| `dynode` | — | `node[dynode]` | `tubes` | `top`, `bottom`, `left`, `right`, `centerarc`, `top left/right` | Phototube/multiplier electrode; styled into anode/photocathode |

### 4. RF Components (§4.17, pp. 158–161)
Center anchor `center` is located on the connection terminal for seamless wiring into paths.
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `bareantenna` | — | `node[bareantenna]` | `RF` | `center`, `top`, `bottom`, `left`, `right` | Modern tailless antenna; connects at `center`; fillable |
| `dinantenna` | — | `node[dinantenna]` | `RF` | `center`, `left`, `right` | German DIN 40900 standard antenna symbol; fillable |
| `bareTXantenna` | — | `node[bareTXantenna]` | `RF` | `center`, `top`, `waves` | Transmit antenna with radiating waves (waves outside bbox); fillable |
| `bareRXantenna` | — | `node[bareRXantenna]` | `RF` | `center`, `top`, `waves` | Receive antenna with radiating waves; fillable |
| `waves` | — | `node[waves]` | `RF` | geographic | Radiating wave decoration for block diagrams (`xscale=2` stretches) |
| `harmonics` | — | `node[harmonics]` | `RF` | geographic | Harmonic wave decoration |
| `mstline` | — | `to[mstline]` (`mstlineshape`) | `RF` | `center`, `left`, `right` | Microstrip transmission line; direct length via `mstlinelen=<len>`; fillable |
| `mslstub` | — | `node[mslstub]` | `RF` | `center`, `left`, `right` | Microstrip open/short linear stub; fillable |
| `msport` | — | `node[msport]` | `RF` | `center`, `left`, `right` | Microstrip launch port; fillable |
| `msrstub` | — | `node[msrstub]` | `RF` | `center`, `left`, `right` | Microstrip radial stub; `monopoles/msrstub/radius=0` suppresses circle; fillable |
| `antenna` | — | `node[antenna]` | `RF` | `center` | Legacy antenna with wire tail |
| `rxantenna` | — | `node[rxantenna]` | `RF` | `center` | Legacy receiving antenna |
| `txantenna` | — | `node[txantenna]` | `RF` | `center` | Legacy transmitting antenna |
| `tlinestub` | — | `node[tlinestub]` | `RF` | `center`, `left`, `right` | Transmission line coaxial stub; fillable |
| `TL` | `transmission line`, `tline` | `to[TL]` (`tlineshape`) | `RF` | `left`, `right`, `top/bottom left/right`, `center left/right` | Transmission line symbol; `bipoles/tline/bare=true` for wire-less; fillable |
| `match` | — | `node[match]` | `RF` | `center`, geographic | RF impedance match block |

### 5. Electro-Mechanical Devices (§4.18, pp. 161–162)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `elmech` | — | `node[elmech]` / `to[Telmech=M, n=id]` | `electromechanicals` | `top`, `bottom`, `left`, `right`, `block north east/west`, `block south east/west`, `block up/down left/right` | Motor (`M`) or generator (`G`); inner circle filled white by default; fillable |

### 6. Double Bipoles & Transformers (§4.19, pp. 162–169)
Port A is left (`A1` up, `A2` down); Port B is right (`B1` up, `B2` down).
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `transformer` | — | `node[transformer]` | `inductors` | `A1`, `A2`, `B1`, `B2`, `base`, `inner dot A1/A2/B1/B2`, `outer dot A1/A2/B1/B2` | 2-winding transformer; shape inherits `cute`, `american`, or `european` inductor style |
| `transformer core` | — | `node[transformer core]` | `inductors` | `A1`, `A2`, `B1`, `B2`, `base`, dot anchors | Transformer with core lines; styled via `transformer core/...` keys |
| `gyrator` | — | `node[gyrator]` | `inductors` | `A1`, `A2`, `B1`, `B2`, `base` | Gyrator (quadpole); class inductors for legacy reasons; fillable |
| `double bipole` | — | `node[double bipole]` | `misc` | `A1`, `A2`, `B1`, `B2`, `base`, fixed dot anchors | Generic configurable dual bipole; sub-bipoles configured via `double bipole L/R` |

### 7. Amplifiers (§4.20, pp. 169–176)
All amplifier shapes belong to class `amplifiers` and are fillable.
Border anchors use prefix `b` (e.g. `b+`, `b-`, `bin up`, `bout`). Polarity-stable anchors `in up`, `in down`, `out up`, `out down` do not flip when `noinv input/output` is toggled.
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `op amp` | — | `node[op amp]` | `amplifiers` | `+`, `-`, `out`, `up`, `down`, `leftedge`, `b+`, `b-`, `bin up/down`, `bout` | Standard operational amplifier |
| `en amp` | — | `node[en amp]` | `amplifiers` | `+`, `-`, `out`, `up`, `down`, border anchors | DIN/EN 60617 op-amp with $\triangleright\infty$; keys `en amp text=`, `en amp text A` |
| `fd op amp` | — | `node[fd op amp]` | `amplifiers` | `+`, `-`, `out +`, `out -`, `up`, `down`, border anchors | Fully differential op-amp with dual complementary outputs |
| `gm amp` | — | `node[gm amp]` | `amplifiers` | `+`, `-`, `out`, `up`, `down`, border anchors | Transconductance amplifier ($G_\mathrm{m}$) |
| `inst amp` | — | `node[inst amp]` | `amplifiers` | `+`, `-`, `out`, `up`, `down`, `refv up`, `refv down` | Instrumentation amplifier with reference terminals |
| `fd inst amp` | — | `node[fd inst amp]` | `amplifiers` | `+`, `-`, `out +`, `out -`, `up`, `down`, `refv up`, `refv down` | Fully differential instrumentation amplifier |
| `inst amp ra` | — | `node[inst amp ra]` | `amplifiers` | same as `inst amp` + `ra+`, `ra-` | Instrumentation amplifier with gain-resistor terminals |
| `plain amp` | — | `node[plain amp]` | `amplifiers` | `in up`, `in down`, `out`, `up`, `down`, `bin up/down`, `bout` | Plain unmarked two-input amplifier block |
| `plain mono amp` | — | `node[plain mono amp]` | `amplifiers` | `in`, `out`, `up`, `bin`, `bout` | Plain single-input amplifier block |
| `buffer` | — | `node[buffer]` | `amplifiers` | `in`, `out`, `up`, `bin`, `bout` | Analog buffer block |
| `plain gm mono amp` | — | `node[plain gm mono amp]` | `amplifiers` | `in`, `out`, `up`, `bin`, `bout` | Plain single-input transconductance amplifier |

### 8. Switches, Buttons & Jumpers (§4.21, pp. 176–187)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `switch` | `spst` | `to[switch]` (`cspstshape`) | `switches` | `left`, `right`, `mid` | Standard open SPST knife switch |
| `closing switch` | `cspst` | `to[closing switch]` | `switches` | `left`, `right`, `mid` | SPST with closing arrow |
| `opening switch` | `ospst` | `to[opening switch]` | `switches` | `left`, `right`, `mid` | SPST with opening arrow |
| `normal open switch` | `nos` | `to[normal open switch]` | `switches` | `left`, `right`, `mid` | Normally open push/contact switch |
| `normal closed switch` | `ncs` | `to[normal closed switch]` | `switches` | `left`, `right`, `mid` | Normally closed switch |
| `opening normal closed switch` | `oncs` | `to[oncs]` | `switches` | `left`, `right`, `mid` | Opening NC switch |
| `closing normal closed switch` | `cncs` | `to[cncs]` | `switches` | `left`, `right`, `mid` | Closing NC switch |
| `opening normal open switch` | `onos` | `to[onos]` | `switches` | `left`, `right`, `mid` | Opening NO switch |
| `closing normal open switch` | `cnos` | `to[cnos]` | `switches` | `left`, `right`, `mid` | Closing NO switch |
| `push button` | `normally open push button`, `nopb` | `to[push button]` | `switches` | `tip`, `mid` | Normally open pushbutton |
| `normally closed push button` | `ncpb` | `to[ncpb]` | `switches` | `tip`, `mid` | Normally closed pushbutton |
| `normally open push button closed` | `nopbc` | `to[nopbc]` | `switches` | `tip`, `mid` | NO pushbutton in depressed/closed state |
| `normally closed push button open` | `ncpbo` | `to[ncpbo]` | `switches` | `tip`, `mid` | NC pushbutton in activated/open state |
| `toggle switch` | — | `to[toggle switch]` | `default` | `in`, `out 1`, `out 2`, `mid` | SPDT toggle switch (to-style) |
| `reed` | — | `to[reed]` (`reedshape`) | `switches` | `left`, `right`, `mid` | Magnetic reed switch; fillable |
| `spdt` | — | `node[spdt]` | `switches` | `in`, `out 1`, `out 2`, `mid` | Node-style SPDT switch |
| `cute closed switch` | `ccsw` | `to[ccsw]` | `switches` | `in`, `out`, `mid` | Cute-style closed switch (size matches cute nodes) |
| `cute open switch` | `cosw` | `to[cosw]` | `switches` | `in`, `out`, `mid` | Cute-style open switch |
| `cute closing switch` | `ccgsw` | `to[ccgsw]` | `switches` | `in`, `out`, `mid` | Cute closing switch with arrow |
| `cute opening switch` | `cogsw` | `to[cogsw]` | `switches` | `in`, `out`, `mid` | Cute opening switch with arrow |
| `cute spdt up` | — | `node[cute spdt up]` | `switches` | `in`, `out 1/2` (border), `cin`, `cout 1/2` (center), `mid` | Cute SPDT in up throw; subnodes `NAME-in`, `NAME-out 1/2` |
| `cute spdt mid` | — | `node[cute spdt mid]` | `switches` | same as `cute spdt up` | Cute SPDT in disconnected middle state |
| `cute spdt down` | — | `node[cute spdt down]` | `switches` | same as `cute spdt up` | Cute SPDT in down throw |
| `cute spdt up arrow` | — | `node[cute spdt up arrow]` | `switches` | same as `cute spdt up` | Cute SPDT up with motion arrow |
| `cute spdt mid arrow` | — | `node[cute spdt mid arrow]` | `switches` | same as `cute spdt up` | Cute SPDT mid with motion arrow |
| `cute spdt down arrow` | — | `node[cute spdt down arrow]` | `switches` | same as `cute spdt up` | Cute SPDT down with motion arrow |
| `proximeter` | — | `node[proximeter]` | `switches` | `center`, `hlines nw/ne/sw/se` | Proximity sensor block shape; fillable |
| `inline proximeter` | — | `to[inline proximeter]` | `switches` | `left`, `right` | Proximity sensor bipole; fillable |
| `rotaryswitch` | `rotary switch` | `node[rotaryswitch]` | `switches` | `in`, `out 1`..`N`, `cin`, `cout 1`..`N`, `mid`, `aout 1`..`N`, `sqout 1`..`N`, `ext north`..`south` | Multipole rotary selector; styles `rotary switch=<ch> in <ang> wiper <w>` |
| `bare jumper` | — | `to[bare jumper]` (`bjumpershape`) | `switches` | `in`, `out`, `top arc` (mid-gap) | Bare PCB jumper (pads only); fillable |
| `open jumper` | — | `to[open jumper]` (`ojumpershape`) | `switches` | `in`, `out`, `top arc` | Open PCB jumper with wire arc; fillable |
| `closed jumper` | — | `to[closed jumper]` (`cjumpershape`) | `switches` | `left`, `right`, `top arc` | Closed PCB jumper with bridged wire; fillable |
| `three-pins jumper` | — | `to[three-pins jumper]` (`tjumpershape`) | `switches` | `in`, `out`, `tap`, `top arc left/right` | 3-pin jumper; configured via `tjumper connections=<val>`; fillable |
| `open solder jumper` | `osj` | `to[open solder jumper]` (`osjumpershape`) | `switches` | `in`, `out` | Open PCB solder bridge |
| `closed solder jumper` | `csj` | `to[closed solder jumper]` (`csjumpershape`) | `switches` | `left`, `right` | Closed PCB solder bridge |
| `open double solder jumper` | `odsj` | `to[odsj]` (`odsjumpershape`) | `switches` | `in`, `out`, `tap` | 3-terminal open double solder jumper |
| `closed double solder jumper` | `cdsj` | `to[cdsj]` (`cdsjumpershape`) | `switches` | `left`, `right` | 3-terminal closed double solder jumper |
| `left double solder jumper` | `ldsj` | `to[ldsj]` (`ldsjumpershape`) | `switches` | `left`, `right`, `tap up`, `tap down` | Solder bridge to left pad |
| `right double solder jumper` | `rdsj` | `to[rdsj]` (`rdsjumpershape`) | `switches` | `left`, `right`, `tap up`, `tap down` | Solder bridge to right pad |

## Family keys (\ctikzset)
### Blocks
`blocks/scale` → scale factor for all block diagram elements (default 1.0)
`blocks/inner color` → stroke color of inner block drawings without changing box color (default default)
`blocks/inner thickness` → line thickness multiplier for inner block drawings (default 1)
`blocks/filter grid/thickness` → relative line thickness of Bode-plot filter grid (default 0.5)
`blocks/filter grid/color` → stroke color of filter grid (default default)
`blocks/filter grid/dash` → dash pattern for filter grid, e.g. {{0.04cm}{0.04cm}} or none (default {{0.04cm}{0.04cm}})
`blocks/filter grid/opacity` → stroke opacity of filter grid; set 0 to suppress grid entirely (default 0.3)
`blocks/filter plot/relative thickness` → relative thickness of frequency response sketch line (default 2)
`blocks/filter plot/rounding` → corner rounding radius of filter curve; set 0 for sharp corners (default 0.15)
`blocks/gridnode/gridlines` → number of parallel diagonal lines in gridnode shape (default 7)
`blocks dc segments` → segment count for DC lower line in converter blocks: 1 continuous, 2 international, 3 English (default 1)
`blocks dc in segments` → segment count for input-side DC line of converter blocks
`blocks dc out segments` → segment count for output-side DC line of converter blocks
`inner blocks dashed` → boolean to dash inner symbols when whole block is dashed (default false)
`dashed blocks pattern` → dash pattern for dashed blocks, e.g. {{1.5pt}{1pt}} (default {{1mm}{1mm}})
`ideal filter` → boolean style forcing sharp corners and linear grid on plot filters
`ideal filter numbers` → boolean to render 0 and 1 annotations on frequency response axis
`ideal filter number low` → custom label for low frequency response axis mark (default 0)
`ideal filter number high` → custom label for high frequency response axis mark (default 1)
`block left anchors pos` → vertical fraction along left edge (0..1) for `left up/down` anchors (default 0.5)
`block right anchors pos` → vertical fraction along right edge (0..1) for `right up/down` anchors (default 0.5)
`block lateral anchors pos` → simultaneous setting for both left and right lateral anchor positions
`bipoles/twoport/width` → width of generic twoport blocks (default 0.7)
`bipoles/twoportsplit/width` → width of generic twoportsplit blocks (default 0.7)

### Transistors
`transistors/scale` → global scale factor for all transistor components (default 1.0)
`transistors/thickness` → line thickness of transistor base and gate segments (default 2)
`transistors/gate thickness` → relative thickness of gate channel segment (default 1)
`transistors/rounded caps` → boolean enabling rounded caps on thick base/gate lines (default false)
`transistors/arrow pos` → position of transistor terminal arrows: end or legacy (default legacy)
`tripoles/mos style` → arrows or no arrows on MOS source/drain terminals (default no arrows)
`tripoles/pmos style/emptycircle` → style to draw PMOS gate circle as non-solid (open)
`tripoles/pmos style/nocircle` → style to remove PMOS gate circle completely (use only with arrowmos)
`tr circle` → boolean enabling circular packaging housing around transistors (default false)
`transistor circle/scale circle radius` → scale multiplier for transistor package circle radius (default 1.0)
`transistor circle/default base in` → relative base entry position for transistor circle (default 0.9)
`transistor circle/njfet base in` → relative base entry position for JFET package circle (default 1.05)
`transistor circle/isfet base in` → relative base entry position for ISFET package circle (default 0.65)
`transistor circle/relative thickness` → line thickness multiplier for transistor circle (default 1.0)
`transistor circle/color` → stroke color of transistor circle (default default)
`transistor circle/dash` → dash pattern for transistor circle, e.g. {{4pt}{4pt}} (default default)
`transistor circle/partial borders` → 4-digit string (0=none, 1=solid, 2=dashed) for circle quadrants (default none)
`transistor circle/partial border dash` → dash pattern for partial circle quadrants (default {{2pt}{2pt}})
`bodydiode` → boolean enabling automatic body/flyback diode across collector-emitter or drain-source (default false)
`transistor bodydiode/relative thickness` → thickness multiplier for body diode lines (default 1.0)
`transistor bodydiode/color` → stroke color of body diode (default default)
`transistor bodydiode/dash` → dash pattern for body diode lines (default default)
`transistor bodydiode/scale` → scale of body diode relative to basic diode dimension (default 0.3)
`transistor bodydiode/dot scale` → scale of body diode connection dots; set 0 to suppress dots (default 0.7)
`transistor solderdot scale` → scale of internal connection solder dots relative to circ pole (default 0.7)
`tripoles/hemt/split gate` → boolean to draw split gate on HEMT devices (default false)
`tripoles/hemt/source arrow` → source arrow on HEMT: 1 for right-facing, -1 for left-facing, 0 for none (default 0)
`tripoles/hemt/gate asym` → asymmetric vertical offset of HEMT gate terminal (default 0.0)
`tripoles/hemt/base height` → height of HEMT base vertical bar (default 0.4)
`tripoles/hemt/gate height` → distance of HEMT source/drain terminals (default 0.4)
`tripoles/hemt/bodydiode conn` → attachment position fraction for body diode on HEMT (default 0.6)
`tripoles/schottky base size` → relative size of Schottky hook on BJT base (default 0.05)
`transistors/modifier thickness` → relative line thickness of ferroelectric gate modifier (default 1)
`tripoles/igbt/outer base height` → relative height of IGBT outer base line (default 0.4)
`tripoles/igbt/outer base thickness` → relative thickness of IGBT outer base line (default 1.0)
`tripoles/bjt/pins width` → length of BJT base/collector/emitter leads; set 0 for bare transistors (default 0.3)
`tripoles/bjt/multi height` → vertical spacing between multiple collectors/emitters on bjtnpn/bjtpnp (default 0.5)
`tripoles/nmosd/depletion color` → fill color of depletion-mode NMOS channel bar; none for unfilled (default default)
`tripoles/pmosd/depletion color` → fill color of depletion-mode PMOS channel bar (default default)
`transistors/jfet gate height` → vertical fraction of gate terminal along JFET channel; 0 is centered (default 1.0)
`tr gap fill` → fill color for insulated gate/base dielectric gap (default none)
`component text` → label placement for transistors (center, up, down) and amplifiers/blocks (center, left, border)

### Electronic Tubes
`tubes/scale` → scale factor for all vacuum tube components (default 1.0)
`tubes/width` → relative width of tube glass envelope (default 1.0)
`tubes/height` → relative height of tube glass envelope (default 1.4)
`tubes/tube radius` → radius of tube envelope curvature (default 0.40)
`tubes/anode distance` → distance of anode plate from envelope center (default 0.40)
`tubes/anode width` → width of anode plate; in matube sets width of all anodes (default 0.40)
`tubes/grid protrusion` → grid lead protrusion length from center (default 0.25)
`tubes/grid dashes` → number of dashes used to draw tube grid lines (default 5)
`tubes/grid separation` → distance between multiple grid levels (default 0.2)
`tubes/grid shift` → vertical offset shift of grids from center (default 0.0)
`tubes/cathode distance` → distance of cathode wire from grid (default 0.40)
`tubes/cathode width` → width of cathode wire (default 0.40)
`tubes/cathode corners` → bevel corner radius of cathode wire (default 0.06)
`tubes/cathode right extend` → horizontal extension of cathode right side (default 0.075)
`tubes/filament distance` → vertical distance of heater filament from cathode (default 0.1)
`tubes/filament angle` → angle of filament wire legs from centerpoint (default 15)
`tubes/thickness` → line thickness multiplier for tubes (default 2)
`tubes/fill` → background fill color inside tube envelope (default none)
`tubes/partial borders` → 6-digit string (0=none, 1=solid, 2=dashed) for tube envelope sectors (default none)
`tubes/partial border dash` → dash pattern for partial tube envelope, e.g. {{2pt}{2pt}} (default {{2pt}{2pt}})
`monopoles/dynode/width` → total width of dynode electrode (default 0.4)
`monopoles/dynode/height` → total height of dynode electrode (default 0.8)
`monopoles/dynode/arc angle` → starting angle for dynode arc; 90 suppresses arc, 0 gives semicircle (default 30)
`monopoles/dynode/arc pos` → vertical center position fraction of dynode arc (default 0.5)
`monopoles/dynode/top width` → relative width of dynode top bar; 0 eliminates bar (default 1.0)
`anodes` → node key setting number of anode pins on matube (default 3)
`anodedot` → node key substituting dots for anode bars in matube
`nixieanode` → node key swapping anode/cathode representation for Nixie displays in matube
`nogrid` → node key suppressing internal grid in matube

### RF Components & Transmission Lines
`RF/scale` → scale factor for all RF components (default 1.0)
`bipoles/mstline/height` → height of microstrip transmission lines and stubs (default 0.3)
`bipoles/mstline/width` → width of microstrip transmission lines (default 1.2)
`monopoles/msport/width` → width of microstrip launch port msport (default 0.5)
`monopoles/msrstub/radius` → radius of msrstub connection circle; 0 suppresses circle (default 0.25)
`mstlinelen` → direct length parameter for mstline path element
`bipoles/tline/width` → length/width of transmission line symbol TL (default 0.6)
`bipoles/tline/bare` → boolean to draw TL without connection wires (default false)

### Electro-Mechanical Devices
`Telmech` → path syntax prefix for motor/generator with node naming via `n=<id>`

### Transformers & Double Bipoles
`quadpoles/transformer/width` → width of transformer quadrupole (default 1.5)
`quadpoles/transformer/height` → height of transformer quadrupole (default 1.5)
`quadpoles/transformer core/core width` → distance between transformer core lines (default 0.05)
`bipoles/cutechoke/cthick` → line thickness of transformer core lines
`transformer core/relative thickness` → thickness multiplier for transformer core lines (default 1.0)
`transformer core/color` → stroke color of transformer core lines (default default)
`transformer core/dash` → dash pattern for transformer core lines, e.g. {{4pt}{2pt}} (default default)
`quadpoles/transformer/inner` → vertical section fraction; setting 1 creates inline transformer (default 0.4)
`quadpoles style` → inward (with horizontal leads, default) or inline (straight vertical leads)
`transformer L1/.style` → TikZ style applied independently to primary coil L1 (e.g. coils count, width)
`transformer L2/.style` → TikZ style applied independently to secondary coil L2
`double bipole L` → shape name for left branch of generic double bipole (default genericshape)
`double bipole R` → shape name for right branch of generic double bipole (default vsourceAMshape)
`double bipole L invert` → boolean to flip vertical orientation of left bipole branch (default false)
`double bipole R invert` → boolean to flip vertical orientation of right bipole branch (default false)
`every double bipole L/.style` → styling applied to left branch of generic double bipole
`every double bipole R/.style` → styling applied to right branch of generic double bipole

### Amplifiers
`amplifiers/scale` → scale factor for all amplifier components (default 1.0)
`amplifiers/plus` → label symbol for non-inverting input terminal (default $+$)
`amplifiers/minus` → label symbol for inverting input terminal (default $-$)
`amp symbol font` → font styling hook for amplifier $+/-$ signs, set via `\tikzset{amp symbol font={...}}`
`tripoles/op amp/width` → total width including leads for standard op amp (default 1.7)
`tripoles/op amp/port width` → relative body size fraction for op amp; setting 1 removes leads (default 0.7)
`noinv input up` → style flipping inputs so non-inverting ($+$) is on top
`noinv input down` → style placing non-inverting ($+$) input on bottom (default)
`noinv output up` → style placing positive output ($+$) on top for fully differential amps (default)
`noinv output down` → style placing positive output ($+$) on bottom
`left text distance` → distance offset for left-aligned amplifier labels (default 0.3em)
`tripoles/en amp/input height` → vertical spacing fraction between inputs on European en amp (default 0.3)
`tripoles/en amp/font` → font for $+/-$ terminal labels on en amp
`tripoles/en amp/font2` → font for internal amplifier symbol on en amp
`en amp text=` → custom inner text for en amp (default $\mathstrut{\triangleright}\,{\infty}$)
`en amp text A` → shortcut setting en amp inner text to $\mathstrut{\triangleright}\,\mathrm{A}$

### Switches, Buttons & Jumpers
`switches/scale` → scale factor for switch and button bodies; does NOT scale poles (default 1.0)
`bipoles/cuteswitch/thickness` → lever thickness of cute switches in ocirc diameters (default 1)
`bipoles/cuteswitch/shape` → contact shape for cute switches, e.g. ocirc, circ (default ocirc)
`proximeter/hlines thickness` → relative thickness of internal horizontal lines in proximeter (default 0.5)
`proximeter/hlines position` → vertical position fraction of horizontal lines in proximeter (default 0.3)
`proximeter/width` → size of proximeter symbol; best configured in preamble (default 0.3)
`multipoles/rotary/channels` → number of throw poles on rotaryswitch (default 5)
`multipoles/rotary/angle` → total angular span in degrees for rotary switch output poles (default 120)
`multipoles/rotary/wiper` → angle of rotary switch wiper lever in degrees (default 20)
`multipoles/rotary/arrow` → arrow on rotary switch wiper: none, cw, ccw, or both (default none)
`multipoles/rotary/shape` → pole terminal shape for rotaryswitch (default ocirc)
`multipoles/rotary/thickness` → wiper lever thickness; negative value suppresses wiper drawing (default 1)
`tripoles/spdt/width` → width parameter determining size of rotary and cute SPDT switches (default 0.85)
`switch end arrow` → arrow tip specification for switch movement arrow (default latexslim)
`switch start arrow` → arrow tip specification for switch start position (default none)
`switch arrows/color` → stroke color of switch movement arrows (default default)
`switch arrows/relative thickness` → thickness multiplier for switch movement arrows (default 1.0)
`switch arrows/dash` → dash pattern for switch arrows, e.g. {{1pt}{1pt}} or none (default default)
`bipoles/jumpers/shape` → terminal contact shape for jumpers, e.g. osquarepole, odiamondpole
`tjumper connections` → two-character code setting 3-pin jumper state (0=bare, 1=open, 2=closed, or S+digit span)

## Traps
1. **Silently ignored transistor options**: Multi-terminal BJTs (`bjtnpn`, `bjtpnp`) silently ignore `photo` and `bodydiode` options without warning or error.
2. **Bounding box label exclusion**: Transistor and tube label texts sit outside the geometric bounding box; TikZ may crop them. Expand bounding boxes manually with `\path (<coord>);` or use separate text nodes.
3. **Mirroring mirrors labels**: Applying `xscale=-1` or `yscale=-1` to a transistor or European amplifier reflects its internal text. Use `\ctikzflipx{<label>}` or `\scalebox{-1}[1]{<text>}` to restore readable orientation.
4. **Moving `right` anchor on circled transistors**: Adding `tr circle` shifts the `right` anchor to the circle perimeter, while all other geographic anchors remain tied to the transistor body.
5. **UJT anchor shifts**: Specifying `nobase` on `nujt`/`pujt` moves the `E`/`emitter` anchor with the omitted lead, but `G` and `kink` anchors remain fixed in place.
6. **Double braces required for dash patterns**: Any key setting dash patterns (`dash`, `partial border dash`, `dashed blocks pattern`) requires double braces, e.g. `dash={{2pt}{2pt}}`. Single braces will be parsed incorrectly by PGF.
7. **Fixed digit counts for partial borders**: `transistor circle/partial borders` requires exactly 4 digits (`0`=omit, `1`=solid, `2`=dashed, numbered 1..4 clockwise from top-right). `tubes/partial borders` requires exactly 6 digits. Any other length causes TeX compilation errors.
8. **Indistinguishable PMOS trap**: A PMOS transistor configured with `nocircle` and without `arrowmos` is visually identical to an NMOS transistor; the manual explicitly warns "DON'T do it".
9. **Transformer coil overflow**: Overriding coil counts or widths via `transformer L1/.style` or `L2/.style` does not auto-resize the transformer body. Coils will overflow unless `quadpoles/transformer/height` is manually increased.
10. **Rotated transformer sub-coils**: Sub-coils `T-L1` and `T-L2` are rotated $-90^\circ$ and $+90^\circ$. Their internal geographic anchors (`north`, `south`, etc.) are rotated sideways.
11. **TikZ-level key prefix requirement**: In `\begin{circuitikz}[...]` environment options or `\tikzset{...}` styles, CircuiTikZ keys must include the `circuitikz/` directory prefix (e.g. `circuitikz/quadpoles style=inline`).
12. **Transistor path annotation limitations**: Path-style transistors (`to[Tnmos]`) do not support voltage (`v=`), current (`i=`), flow (`f=`), or annotation (`a=`) syntax, and automatic label rotation can fail; prefer node style for labeled transistors.
13. **Switch scaling preserves pole size**: `switches/scale` scales the switch lever and body length, but does NOT scale the pole terminal circles (configured separately under pole keys).
14. **Amplifier polarity anchor shifts**: Using `noinv input up` flips the physical locations of anchors `+` and `-`. To connect to terminals irrespective of sign flip, use position-stable anchors `in up` and `in down`.
15. **Rotary switch name vs. style spacing**: The node shape name is `rotaryswitch` (strictly NO spaces); parameter helper styles are `rotary switch=<ch> in <ang> wiper <w>` (WITH spaces). Changing `multipoles/rotary/*` defaults does not alter the parameterized styles.
16. **Two-character constraint on tjumper connections**: The value of `tjumper connections` MUST be exactly two characters (e.g. `00`, `12`, `21`, `S1`), otherwise compilation fails.
17. **Depletion MOSFET channel color**: `tripoles/nmosd/depletion color=none` leaves the channel bar unfilled and will not adopt the path drawing color.
18. **Cute switch contact alignment**: Customizing `bipoles/cuteswitch/shape` to non-circular shapes misaligns default `in`/`out` anchors; use internal contact node centers `cin`, `cout 1`, `cout 2`.

Cross-checked against manual index: 197 names verified.
