# Passives, Sources, Instruments, and Lines (§4.1–§4.13, pp. 55–110)
> Read this when drawing: grounds, power rails, resistors, potentiometers, capacitors, inductors, diodes, thyristors, voltage/current sources, instruments, probes, mechanical analogs, fuses, lamps, acoustic transducers, buses, crossings, arrows, terminal poles, and connectors.

## Syntax recap
Canonical verbatim example from manual (§4.2.1, p. 60):
```latex
\begin{circuitikz}[european]
\draw (0,0) to[battery2, l=E] ++(0,4.5)
-- ++(2,0) coordinate(tmp)
to[vR, l2_=$P_1$ and \SI{10}{\kohm}, mirror,
invert, name=P]
(0,0-|tmp) -- (0,0);
\draw (0,0-|tmp) -- ++(1.5,0)
to[R=$R_1$, -*] ++(0,2) coordinate(p)
|- (P.wiper);
\draw (p) to[rmeterwa, t=V] (tmp-|p) -- (tmp);
\end{circuitikz}
```
Orientation:
- Two placement styles: path bipoles `to[Component, options] (coord)` and standalone shapes `node[NodeShape, options] (name) {text}`. Bipoles can be placed as nodes by appending `shape` to the name (e.g. `node[potentiometershape]`).
- Terminal access for multi-terminal bipoles (potentiometer wiper, thyristor gate, transformer taps) requires naming the bipole: `to[pR, n=POT]` exposes anchor `(POT.wiper)` or `(POT.W)`; `to[Ty, n=SCR]` exposes `(SCR.G)` or `(SCR.gate)`.
- Never use TikZ `scale`, `xscale`, or `yscale` inside `to[...]`; use `mirror` (flip across path) and `invert` (swap electrical polarity).
- Solder dots and hollow terminals attach directly via path pole shorthands `-*`, `*-`, `*-*`, `-o`, `o-`, `o-o`.

## Component inventory

### Grounds and Supply Voltages (§4.1, pp. 56–57)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `ground` | — | `node[ground]` | `grounds` | `center`, `north` | Earth ground; `center` = `north` (connection point); hangs downward |
| `tlground` | — | `node[tlground]` | `grounds` | `center`, `north` | Tailless ground |
| `rground` | — | `node[rground]` | `grounds` | `center`, `north` | Reference ground |
| `sground` | — | `node[sground]` | `grounds` | `center`, `north` | Signal ground; fillable |
| `tground` | — | `node[tground]` | `grounds` | `center`, `north` | Thicker tailless reference ground |
| `nground` | — | `node[nground]` | `grounds` | `center`, `north` | Noiseless ground |
| `pground` | — | `node[pground]` | `grounds` | `center`, `north` | Protective ground; fillable |
| `cground` | — | `node[cground]` | `grounds` | `center`, `north` | Chassis ground |
| `eground` | — | `node[eground]` | `grounds` | `center`, `north` | European style ground |
| `eground2` | — | `node[eground2]` | `grounds` | `center`, `north` | European style ground v2 |
| `vcc` | — | `node[vcc]` | `power supplies` | `center`, `south` | Positive power supply rail ($V_{CC}/V_{DD}$); arrow points upward |
| `vee` | — | `node[vee]` | `power supplies` | `center`, `north` | Negative power supply rail ($V_{EE}/V_{SS}$); arrow points downward |

### Resistive Bipoles & Potentiometers (§4.2, pp. 57–63)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `short` | — | `to[short]` | `default` | path ends | Wire segment supporting labels, currents, and pole dots (`-*`, `-o`) |
| `open` | — | `to[open]` | `default` | path ends | Open circuit path placeholder for invisible branch connections |
| `generic` | — | both (`genericshape`) | `resistors` | path ends | Generic rectangular box bipole; fillable |
| `xgeneric` | — | both (`xgenericshape`) | `resistors` | path ends | Crossed generic box; fillable |
| `sgeneric` | — | both (`sgenericshape`) | `resistors` | path ends | Slashed generic box; fillable |
| `singeneric` | — | both (`singenericshape`) | `resistors` | path ends | Sine-wave generic box; fillable |
| `tgeneric` | — | both (`tgenericshape`) | `resistors` | `wiper`, `tip` | Tunable generic box (arrow through box); fillable |
| `ageneric` | — | both (`agenericshape`) | `resistors` | path ends | Asymmetric generic box; fillable |
| `memristor` | `Mr` | both (`memristorshape`) | `resistors` | path ends | Memristor bipole; fillable |
| `R` | `american resistor`, `european resistor` | both (`resistorshape` / `genericshape`) | `resistors` | path ends | Resistor; zigzag under `americanresistors` (default), box under `europeanresistors` |
| `vR` | `variable american resistor`, `variable european resistor` | both (`vresistorshape` / `tgenericshape`) | `resistors` | `wiper`, `tip` | Variable resistor (tunable arrow across body) |
| `pR` | `american potentiometer`, `european potentiometer` | both (`potentiometershape` / `genericpotentiometershape`) | `resistors` | `wiper` (`W`), `tip`, `wiper label` | Potentiometer; arrow taps center of resistive element |
| `sR` | `american resistive sensor`, `european resistive sensor` | both (`resistivesensshape` / `thermistorshape`) | `resistors` | `label` | Resistive sensor; `label` anchor at corner of slash line |
| `ldR` | `american light dependent resistor`, `european light dependent resistor` | both (`ldresistorshape` / `ldgenericshape`) | `resistors` | path ends | Light-dependent resistor (LDR); incoming opto arrows; fillable |
| `varistor` | — | both (`varistorshape`) | `resistors` | path ends | Voltage-dependent resistor (varistor, non-linear); fillable |
| `mov` | — | both (`movshape`) | `resistors` | path ends | Metal-oxide varistor (MOV); fillable |
| `phR` | `photoresistor` | both (`photoresistorshape`) | `resistors` | path ends | Photoresistor; incoming opto arrows; fillable |
| `thR` | `thermistor` | both (`thermistorshape`) | `resistors` | path ends | Thermistor; fillable |
| `thRp` | `thermistor ptc` | both (`thermistorptcshape`) | `resistors` | path ends | PTC thermistor ($+\vartheta$ label); fillable |
| `thRn` | `thermistor ntc` | both (`thermistorntcshape`) | `resistors` | path ends | NTC thermistor ($-\vartheta$ label); fillable |

### Capacitors and Inductors (§4.3, pp. 64–70)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `C` | `capacitor` | both (`capacitorshape`) | `capacitors` | path ends | Standard parallel-plate capacitor; fillable since v1.4.1 |
| `cC` | `curved capacitor` | both (`ccapacitorshape`) | `capacitors` | path ends | Curved-plate polarized capacitor; fillable |
| `eC` | `ecapacitor`, `elko` | both (`ecapacitorshape`) | `capacitors` | path ends | Electrolytic capacitor with positive plate polarity mark; fillable |
| `vC` | `variable capacitor` | both (`vcapacitorshape`) | `capacitors` | `wiper`, `tip` | Variable capacitor (diagonal tunable arrow); fillable |
| `sC` | `capacitive sensor` | both (`capacitivesensshape`) | `capacitors` | `label` | Capacitive sensor; `label` anchor at corner of slash |
| `PZ` | `piezoelectric` | both (`piezoelectricshape`) | `capacitors` | path ends | Piezoelectric crystal element; default width $2\times$ standard; fillable |
| `cpe` | — | both (`cpeshape`) | `capacitors` | path ends | Constant Phase Element; fillable |
| `feC` | `ferrocap` | both (`ferrocapshape`) | `capacitors` | `kink left/right`, `curve left/right` | Ferroelectric capacitor with hysteresis loop; fillable |
| `L` | `inductor`, `cute inductor`, `american inductor`, `european inductor` | both (`cuteinductorshape`, `americaninductorshape`, `fullgenericshape`) | `inductors` | `midtap`, `core west/east`, `ul/ur/ll/lr dot` | Inductor; `cuteinductors` (looped default), `americaninductors` (semi-circles), `europeaninductors` (filled box) |
| `vL` | `variable cute inductor`, `variable american inductor`, `variable european inductor` | both (`vcuteinductorshape`, `vamericaninductorshape`, `tfullgenericshape`) | `inductors` | `wiper`, `tip`, `midtap`, `core west/east` | Variable inductor (tunable arrow) |
| `sL` | `cute inductive sensor`, `american inductive sensor`, `european inductive sensor` | both (`scuteinductorshape`, `samericaninductorshape`, `sfullgenericshape`) | `inductors` | `label`, `midtap`, `core west/east` | Inductive sensor; `label` anchor at corner of slash |
| `cute choke` | — | both (`cutechokeshape`) | `inductors` | `midtap`, `core west/east` | Choke with core line; options `onelinechoke`, `twolineschoke` |

### Diodes, Thyristors, and Opto Devices (§4.4, pp. 70–78)
Fill-variant suffixes: bare = default style (`empty diodes`); `o` = forced empty (fillable); `*` = forced full (solid fill); `-` = forced stroke (outline with through-stroke).
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `D` | `Do`, `D*`, `D-`, `empty diode`, `full diode`, `stroke diode` | both (`emptydiodeshape`, `fulldiodeshape`) | `diodes` | path ends | Standard PN junction diode |
| `sD` | `sDo`, `sD*`, `sD-`, `empty Schottky diode`, `full Schottky diode`, `stroke Schottky diode` | both (`emptysdiodeshape`, `fullsdiodeshape`) | `diodes` | path ends | Schottky diode (S-hook cathode) |
| `zD` | `zDo`, `zD*`, `zD-`, `empty Zener diode`, `full Zener diode`, `stroke Zener diode` | both (`emptyzdiodeshape`, `fullzdiodeshape`) | `diodes` | path ends | Zener diode (bent cathode bar) |
| `zzD` | `zzDo`, `zzD*`, `zzD-`, `empty ZZener diode`, `full ZZener diode`, `stroke ZZener diode` | both (`emptyzzdiodeshape`, `fullzzdiodeshape`) | `diodes` | path ends | Double / bidirectional Zener diode |
| `tD` | `tDo`, `tD*`, `tD-`, `empty tunnel diode`, `full tunnel diode`, `stroke tunnel diode` | both (`emptytdiodeshape`, `fulltdiodeshape`) | `diodes` | path ends | Tunnel diode |
| `pD` | `pDo`, `pD*`, `pD-`, `empty photodiode`, `full photodiode`, `stroke photodiode` | both (`emptypdiodeshape`, `fullpdiodeshape`) | `diodes` | path ends | Photodiode; optical arrows point toward junction |
| `leD` | `leDo`, `leD*`, `leD-`, `empty led`, `full led`, `stroke led` | both (`emptylediodeshape`, `fulllediodeshape`) | `diodes` | path ends | Light-Emitting Diode (LED); optical arrows point outward |
| `lasD` | `lasD*`, `lasD-`, `empty laser diode`, `full laser diode`, `stroke laser diode` | both (`emptylaserdiodeshape`, `fulllaserdiodeshape`) | `diodes` | path ends | Laser diode |
| `VC` | `VCo`, `VC*`, `VC-`, `empty varcap`, `full varcap`, `stroke varcap` | both (`emptyvarcapshape`, `fullvarcapshape`) | `diodes` | path ends | Varactor / variable capacitance diode |
| `tvsD` | `tvsDo`, `tvsD*`, `tvsD-`, `empty TVS diode`, `full TVS diode`, `stroke TVS diode` | both (`emptytvsdiodeshape`, `fulltvsdiodeshape`) | `diodes` | path ends | TVS diode / transorb |
| `shD` | `shDo`, `shD*`, `empty Shockley diode`, `full Shockley diode` | both (`emptyshdiodeshape`, `fullshdiodeshape`) | `diodes` | path ends | Shockley four-layer diode |
| `biD` | `biDo`, `biD*`, `empty bidirectionaldiode`, `full bidirectionaldiode` | both (`emptybidirectionaldiodeshape`, `fullbidirectionaldiodeshape`) | `diodes` | path ends | Bidirectional diode / DIAC |
| `Tr` | `Tro`, `Tr*`, `triac`, `empty triac`, `full triac` | both (`emptytriacshape`, `fulltriacshape`) | `diodes` | `anode`, `cathode`, `G` (`gate`) | TRIAC; third gate terminal accessible via `n=NAME` then `(NAME.G)` |
| `Ty` | `Tyo`, `Ty*`, `Ty-`, `thyristor`, `empty thyristor`, `full thyristor`, `stroke thyristor` | both (`emptythyristorshape`, `fullthyristorshape`) | `diodes` | `anode`, `cathode`, `G` (`gate`) | Thyristor (SCR); third gate terminal at `(NAME.G)` |
| `PUT` | `PUTo`, `PUT*`, `PUT-`, `put`, `empty put`, `full put`, `stroke put` | both (`emptyputshape`, `fullputshape`) | `diodes` | `anode`, `cathode`, `G` (`gate`) | Programmable Unijunction Transistor |
| `GTO` | `GTOo`, `GTO*`, `GTO-`, `gto`, `empty gto`, `full gto`, `stroke gto` | both (`emptygtoshape`, `fullgtoshape`) | `diodes` | `anode`, `cathode`, `G` (`gate`) | Gate Turn-Off thyristor |
| `GTOb` | `GTObo`, `GTOb*`, `GTOb-`, `gtobar`, `empty gtobar`, `full gtobar`, `stroke gtobar` | both (`emptygtobarshape`, `fullgtobarshape`) | `diodes` | `anode`, `cathode`, `G` (`gate`) | GTO thyristor with bar-type gate |
| `aGTOb` | `aGTObo`, `aGTOb*`, `aGTOb-`, `agtobar`, `empty agtobar`, `full agtobar`, `stroke agtobar` | both (`emptyagtobarshape`, `fullagtobarshape`) | `diodes` | `anode`, `cathode`, `G` (`gate`) | GTO thyristor with bar-type gate on anode |
| `IGCT` | `IGCTo`, `IGCT*`, `IGCT-`, `igct`, `empty igct`, `full igct`, `stroke igct` | both (`emptyigctshape`, `fulligctshape`) | `diodes` | `anode`, `cathode`, `G` (`gate`) | Integrated Gate-Commutated Thyristor |

### Sources and Generators (§4.5, pp. 78–89)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `battery` | — | both (`batteryshape`) | `batteries` | path ends | Multi-cell DC battery |
| `battery1` | — | both (`battery1shape`) | `batteries` | path ends | Single-cell DC battery |
| `battery2` | — | both (`battery2shape`) | `batteries` | path ends | Single-cell DC battery (alternate proportions) |
| `solar` | — | both (`solarshape`) | `batteries` | path ends | Solar cell battery with optical arrows |
| `baertty` | — | both (`baerttyshape`) | `batteries` | path ends | Randall Munroe xkcd battery bipole |
| `vsource` | `vsourceEU`, `european voltage source`, `V` | both (`vsourceshape`) | `sources` | path ends | European independent voltage source; circle with line; fillable |
| `vsourceC` | `ceV`, `cute european voltage source` | both (`vsourceCshape`) | `sources` | path ends | Cute European voltage source; fillable |
| `vsourceAM` | `american voltage source` | both (`vsourceAMshape`) | `sources` | path ends | American independent voltage source (circle with $+$ and $-$); fillable |
| `isource` | `isourceEU`, `european current source`, `I`, `current source` | both (`isourceshape`) | `sources` | path ends | European independent current source; circle with line arrow; fillable |
| `isourceC` | `ceI`, `cute european current source` | both (`isourceCshape`) | `sources` | path ends | Cute European current source; fillable |
| `isourceAM` | `american current source` | both (`isourceAMshape`) | `sources` | path ends | American independent current source (circle with arrow); fillable |
| `vsourcesin` | `sV`, `sinusoidal voltage source` | both (`vsourcesinshape`) | `sources` | path ends | Sinusoidal AC voltage source (tilde inside); fillable |
| `isourcesin` | `sI`, `sinusoidal current source` | both (`isourcesinshape`) | `sources` | path ends | Sinusoidal AC current source; configurable angle; fillable |
| `vsourceN` | `nV`, `noise voltage source` | both (`vsourceNshape`) | `sources` | path ends | Random noise voltage source; gray filled by default |
| `isourceN` | `nI`, `noise current source` | both (`isourceNshape`) | `sources` | path ends | Random noise current source; gray filled by default |
| `vsourcesquare` | `sqV`, `square voltage source` | both (`vsourcesquareshape`) | `sources` | path ends | Square-wave voltage generator; fillable |
| `vsourcetri` | `tV` | both (`vsourcetrishape`) | `sources` | path ends | Triangle-wave voltage generator; fillable |
| `esource` | — | both (`esourceshape`) | `sources` | path ends | Empty round source (unadorned circle); fillable |
| `pvsource` | — | both (`pvsourceshape`) | `sources` | path ends | Photovoltaic voltage source; fillable |
| `pvmodule` | — | both (`pvmoduleshape`) | `sources` | path ends | Photovoltaic module source; fillable |
| `ioosource` | — | both (`oosourceshape`) | `sources` | path ends | Double-zero overlapping circles current source; fillable |
| `voosource` | — | both (`oosourceshape`) | `sources` | path ends | Double-zero overlapping circles voltage source; fillable |
| `oosourceatrans` | — | both (`oosourceatransshape`) | `sources` | `centerprim`, `primup/down` | Auto-transformer overlapping circles source; fillable |
| `oosourcetrans` | — | both (`oosourcetransshape`) | `sources` | `centerprim/sec`, `primup/down`, `secup/down`, `symbolprim/sec`, `uppertop`, `centerupper`, `symbolupper` | Two-winding transformer source; vector groups: `prim=...`, `sec=...`, `upper=...`; fillable |
| `ooosource` | — | both (`ooosourceshape`) | `sources` | `prim1..2`, `sec1..3`, `tert1..3`, `centerprim/sec/tert`, `symbolprim/sec/tert` | Three-winding transformer source; vector groups `prim`, `sec`, `tert`; fillable |
| `nullator` | — | both (`nullatorshape`) | `sources` | path ends | Nullator (ideal amplifier null element: $V=0, I=0$); fillable |
| `norator` | — | both (`noratorshape`) | `sources` | path ends | Norator (arbitrary $V, I$); fillable |
| `dcvsource` | — | both (`dcvsourceshape`) | `sources` | path ends | DC voltage source (split circle / parallel bars); fillable |
| `dcisource` | — | both (`dcisourceshape`) | `sources` | path ends | DC current source (broken circle with arrow); fillable |
| `cvsource` | `cvsourceEU`, `controlled voltage source` | both (`cvsourceshape`) | `csources` | path ends | Controlled voltage source (European diamond); fillable |
| `cvsourceC` | `cceV`, `cute european controlled voltage source` | both (`cvsourceCshape`) | `csources` | path ends | Cute European controlled voltage source; fillable |
| `cvsourceAM` | `american controlled voltage source` | both (`cvsourceAMshape`) | `csources` | path ends | American controlled voltage source (diamond with $+$ and $-$); fillable |
| `cisource` | `cisourceEU`, `controlled current source` | both (`cisourceshape`) | `csources` | path ends | Controlled current source (European diamond); fillable |
| `cisourceC` | `cceI`, `cute european controlled current source` | both (`cisourceCshape`) | `csources` | path ends | Cute European controlled current source; fillable |
| `cisourceAM` | `american controlled current source` | both (`cisourceAMshape`) | `csources` | path ends | American controlled current source (diamond with arrow); fillable |
| `ecsource` | `empty controlled source` | both (`ecsourceshape`) | `csources` | path ends | Empty diamond controlled source; fillable |
| `cvsourcesin` | `csV`, `controlled vsourcesin`, `controlled sinusoidal voltage source` | both (`cvsourcesinshape`) | `csources` | path ends | Controlled sinusoidal voltage source (diamond with tilde); fillable |
| `cisourcesin` | `csI`, `controlled isourcesin`, `controlled sinusoidal current source` | both (`cisourcesinshape`) | `csources` | path ends | Controlled sinusoidal current source; fillable |

### Instruments & Probes (§4.6, pp. 89–97)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `rmeter` | — | both (`rmetershape`) | `instruments` | `left`, `right`, `center` | Round meter; set interior symbol via `t=...`; fillable |
| `rmeterwa` | — | both (`rmeterwashape`) | `instruments` | `left`, `right`, `center` | Round meter with pointer arrow; set symbol via `t=...`; fillable |
| `ammeter` | — | both (`ammetershape`) | `instruments` | `left`, `right`, `center` | Legacy ammeter ($A$); horizontal only; fillable |
| `voltmeter` | — | both (`voltmetershape`) | `instruments` | `left`, `right`, `center` | Legacy voltmeter ($V$); horizontal only; fillable |
| `ohmmeter` | — | both (`ohmmetershape`) | `instruments` | `left`, `right`, `center` | Legacy ohmmeter ($\Omega$); horizontal only; fillable |
| `smeter` | — | both (`smetershape`) | `instruments` | `left`, `right`, `center`, `in 1`, `in 2` | Square meter; set symbol via `t=...`; fillable |
| `qiprobe` | — | both (`qiprobeshape`) | `instruments` | `left`, `right`, `center` | QUCS-style in-series current probe; fillable |
| `qvprobe` | — | both (`qvprobeshape`) | `instruments` | `left`, `right`, `center` | QUCS-style in-parallel voltage probe; fillable |
| `qpprobe` | — | both (`qpprobeshape`) | `instruments` | `left`, `right`, `center`, `v+`, `v-` | QUCS-style power probe; four terminals (series $I$, sense $v+, v-$); fillable |
| `oscope` | — | both (`oscopeshape`) | `instruments` | `left`, `right`, `in 1`, `in 2` | Oscilloscope display; waveforms via `bipoles/oscope/waveform`; fillable |
| `iloop` | — | both (`iloopshape`) | `instruments` | `left`, `right`, `center`, `i` | Current loop (symbolic test point); probe anchor `i` |
| `iloop2` | — | both (`iloop2shape`) | `instruments` | `left`, `right`, `center`, `i+`, `i-` | Current loop (real physical probe); differential sense anchors `i+`, `i-` |
| `currtap` | — | both (`currtapshape`) | `instruments` | path ends, `tap` | Current tap / contact probe on wire; probe branch connects at `(NAME.tap)` |

### Mechanical Analogy (§4.7, p. 97)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `damper` | — | both (`dampershape`) | `mechanicals` | path ends | Mechanical damping / dashpot; fillable |
| `inerter` | — | both (`inertershape`) | `mechanicals` | path ends | Mechanical inerter; fillable |
| `spring` | — | both (`springshape`) | `mechanicals` | path ends | Mechanical stiffness / coil spring |
| `viscoe` | — | both (`viscoeshape`) | `mechanicals` | `left`, `right`, `center` | Viscoelastic element (parallel spring + dashpot); fillable |
| `mass` | — | both (`massshape`) | `mechanicals` | path ends | Mechanical mass block; fillable |

### Miscellaneous Bipoles & Waveform Symbols (§4.8, pp. 97–102)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `thermocouple` | — | both (`thermocoupleshape`) | `misc` | path ends | Thermocouple temperature sensor junction |
| `fuse` | — | both (`fuseshape`) | `misc` | path ends | Standard IEC fuse (box with continuous through-wire); fillable |
| `afuse` | `asymmetric fuse` | both (`afuseshape`) | `misc` | path ends | Asymmetric fuse; fillable |
| `wfuse` | `wiggly fuse` | both (`wfuseshape`) | `misc` | `B-left`, `B-right` | Wiggly fuse wire; pole subnodes `NAME-left` and `NAME-right` |
| `relais` | — | both (`relaisshape`) | `misc` | path ends | Relay coil (slashed box); fillable |
| `squid` | — | both (`squidshape`) | `misc` | path ends | Superconducting Quantum Interference Device (SQUID) |
| `barrier` | — | both (`barriershape`) | `misc` | path ends | Tunneling barrier / insulating barrier |
| `openbarrier` | — | both (`openbarriershape`) | `misc` | path ends | Open barrier with configurable gap width |
| `european gas filled surge arrester` | `gf surge arrester` [body p. 97], `gas filled surge arrester` | both (`european gas filled surge arrestershape`) | `misc` | path ends | European gas-filled surge arrester; fillable |
| `american gas filled surge arrester` | `gf surge arrester` [body p. 97], `gas filled surge arrester` | both (`american gas filled surge arrestershape`) | `misc` | path ends | American gas-filled surge arrester; fillable |
| `lamp` | — | both (`lampshape`) | `misc` | path ends | Indicator lamp (circle with cross); fillable |
| `bulb` | — | both (`bulbshape`) | `misc` | path ends | Incandescent bulb (loop inside circle); fillable |
| `wbulb` | — | both (`wbulbshape`) | `misc` | path ends | Wiggly filament bulb; fillable |
| `neonlampcc` | — | both (`neonlampccshape`) | `misc` | path ends | Neon lamp with double cathode; fillable |
| `neonlampac` | — | both (`neonlampacshape`) | `misc` | path ends | Neon lamp with anode and cathode; fillable |
| `sparkgap` | — | both (`sparkgapshape`) | `misc` | path ends | Spark gap; options `sparkgap/circle`, `sparkgap/dot`; fillable |
| `loudspeaker` | — | both (`loudspeakershape`) | `misc` | `left`, `right`, `north`, `south`, `east`, `west`, `nw`, `ne`, `sw`, `se` | Loudspeaker sound transducer; fillable |
| `mic` | — | both (`micshape`) | `misc` | `left`, `right`, `north`, `south`, `east`, `west` | Microphone with protruding tail lead; fillable |
| `tlmic` | — | both (`tlmicshape`) | `misc` | `left`, `right`, `north`, `south`, `east`, `west` | Tail-less microphone (inline); fillable; standalone shape `tlmicshape` |
| `buzzer` | — | both (`buzzershape`) | `misc` | `left`, `right` | Buzzer sounder; fillable |
| `rbuzzer` | — | both (`rbuzzershape`) | `misc` | `left`, `right` | Reversed buzzer; fillable |
| `dc symbol` | — | `node[dc symbol]{text}` | `misc` | `left`, `right`, `center` | DC waveform annotation node (solid line over dashed line) |
| `ac symbol` | — | `node[ac symbol]{text}` | `misc` | `left`, `right`, `center` | AC waveform annotation node (sine tilde) |

### Multiple Wires / Buses (§4.9, pp. 102–103)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `multiwire` | — | `to[multiwire=N]` (`multiwireshape`) | `default` | path ends | Single-line bus with angled slash and wire count $N$ |
| `bmultiwire` | — | `to[bmultiwire=N]` (`bmultiwireshape`) | `default` | path ends | Double-line bus with angled slash and wire count $N$ |
| `tmultiwire` | — | `to[tmultiwire=N]` (`tmultiwireshape`) | `default` | path ends | Triple-line bus with angled slash and wire count $N$ |

### Crossings (§4.10, pp. 103–105)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `crossing` | `xing` | `to[crossing]` (`crossingshape`) | `default` | path ends | Jumper-style non-contact wire bridge placed at midpoint of path |
| `jump crossing` | — | `node[jump crossing]` | none | `north`, `south`, `east`, `west`, `center` | Jumper bridge node with gap in straight wire to show crossing |
| `plain crossing` | — | `node[plain crossing]` | none | `north`, `south`, `east`, `west`, `center` | Plain crossing node with gap in straight wire to enhance crossing depth |

### Arrows (§4.11, pp. 105–107)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `currarrow` | — | `node[currarrow]` | none | `center`, `tip` | Current and voltage pseudo-arrow shape |
| `inputarrow` | — | `node[inputarrow]` | none | `center`, `tip` | Block diagram input arrow; anchored at its tip |
| `trarrow` | — | `node[trarrow]` | none | `center`, `tip`, `btip` | Transistor-style filled arrow; `tip` at point, `btip` slightly receded |
| `flowarrow` | — | `node[flowarrow]{text}` | none | `center`, `west`, `east`, `south` | Flow indicator arrow carrying label text |
| `latexslim` | — | arrow tip (`-latexslim`) | none | path ends | Real arrow tip replacement for deprecated TikZ `latex'` |
| `Jack Tap` | `-v`, `^-`, `-vf`, `-^f` | arrow tip (`-{Jack Tap[...]}`) | none | path ends | Real arrow tip for jack audio connectors; auto-reverses at path start |
| `\ctikztunablearrow` | — | macro | none | targets named node | Macro: `\ctikztunablearrow[opts]{thickness}{length}{angle}{name}` |

### Terminal Shapes (§4.12, pp. 107–108)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `circ` | — | `node[circ]` | none | `center`, geographic | Connected solder dot terminal (filled circle) |
| `ocirc` | — | `node[ocirc]` | none | `center`, geographic | Unconnected terminal (open circle filled with `open nodes fill`, default white) |
| `diamondpole` | — | `node[diamondpole]` | none | `center`, geographic | Filled diamond terminal |
| `odiamondpole` | — | `node[odiamondpole]` | none | `center`, geographic | Open diamond terminal (filled with `open nodes fill`) |
| `squarepole` | — | `node[squarepole]` | none | `center`, geographic | Filled square terminal |
| `osquarepole` | — | `node[osquarepole]` | none | `center`, geographic | Open square terminal (filled with `open nodes fill`) |
| `rectjoinfill` | — | `node[rectjoinfill]` | none | `center`, geographic | Corner-fill square matching wire line width (fixes joint gaps) |

### Connectors (§4.13, pp. 108–110)
| Component | Aliases | Style | Class | Key anchors | Notes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `bnc` | — | `node[bnc]` | `connectors` | `left`, `right`, `hot`, `shield`, `center`, `zero` | BNC coaxial connector; `zero` anchor rotates around true geometric center; fillable |
| `iec connector` | `iecconn` | `to[iec connector]` (`iecconnshape`) | `connectors` | path ends, `plug center`, `socket center`, `nw`, `ne` | IEC 60617 combined plug-and-socket bipole |
| `iecconnshape` | — | `node[iecconnshape]{text}` | `connectors` | `left`, `right`, `center` | IEC 60617 combined connector node with label |
| `iecsocketL` | — | `node[iecsocketL]{text}` | `connectors` | `left`, `right`, `center`, `socket center` | IEC 60617 female socket on left side of split connection |
| `iecsocketR` | — | `node[iecsocketR]{text}` | `connectors` | `left`, `right`, `center`, `socket center` | IEC 60617 female socket on right side of split connection |
| `iecplugL` | — | `node[iecplugL]{text}` | `connectors` | `left`, `right`, `center`, `plug center` | IEC 60617 male plug on left side of split connection |
| `iecplugR` | — | `node[iecplugR]{text}` | `connectors` | `left`, `right`, `center`, `plug center` | IEC 60617 male plug on right side of split connection |

## Family keys (\ctikzset)
- `grounds/scale` → scale all ground symbols (default 1.0)
- `power supplies/scale` → scale power supply symbols (default 1.0)
- `monopoles/vcc/arrow` → TikZ `arrows.meta` specification for positive rail arrow (default `legacy`)
- `monopoles/vee/arrow` → TikZ `arrows.meta` specification for negative rail arrow (default `legacy`)
- `resistors/scale` → scale all resistive bipoles (default 1.0)
- `resistors/width` → width of resistor shapes relative to bipole length (default 0.8)
- `resistors/zigs` → number of zigzag peaks in American resistors (default 3)
- `resistors/thickness` → line thickness of resistors relative to base line width (default none = `bipoles/thickness`)
- `resistors/modifier thickness` → thickness of modifier arrows and bars relative to component line thickness (default 1.0)
- `resistors/zigzag hook/.code={\pgfsetroundcap\pgfsetroundjoin}` → PGF join fix for thick American resistors (default void)
- `resistors/zigzag stub` → horizontal leading wire stub fraction to prevent corner clipping at thickness > 2 (default 0)
- `wiper pos` → relative position of potentiometer wiper along resistive body [0, 1] (default 0.5)
- `tunable start arrow` / `tunable end arrow` → arrow tips for variable resistors/capacitors/inductors (default none / `latexslim`)
- `wiper start arrow` / `wiper end arrow` → arrow tips for potentiometer wiper terminal (default none / `latexslim`)
- `capacitors/scale` → scale all capacitor bipoles (default 1.0)
- `capacitors/thickness` → line thickness of capacitors (default none)
- `capacitors/modifier thickness` → thickness of arrows and sensor bars relative to capacitor plates
- `capacitors/width` → distance between capacitor plates relative to basic length (default 0.2; PZ and cpe default $2\times$)
- `capacitors/height` → plate height of capacitors (default 0.6; electrolytic 0.5, piezoelectric 0.7)
- `inductors/scale` → scale all inductor bipoles (default 1.0)
- `inductors/thickness` → line thickness of inductors (default none)
- `inductors/width` → length occupied by coils (default 0.8 American/European, 0.6 Cute)
- `inductors/coils` → number of loops/coils (default 5 cute, 4 American; minimum 2 cute, 1 American)
- `bipoles/cutechoke/cthick` → relative line thickness of choke core lines relative to inductor
- `onelinechoke` / `twolineschoke` → select single or double magnetic core line on `cute choke`
- `bipoles/inductors/core distance` → distance between inductor coils and manual core line anchors `core west/east` (default 2pt)
- `bipoles/inductors/dot x distance` → horizontal extension of mutual inductance dot anchors `ul/ur/ll/lr dot` (default 4pt)
- `bipoles/inductors/dot y distance` → vertical extension of mutual inductance dot anchors (default 1pt)
- `diode=empty|full|stroke` → set default fill rendering for bare diode names (default `empty`)
- `diodes/scale` → scale all diodes and thyristors (default 1.0; author recommends 0.6)
- `thyristor style=legacy|compact` → switch between wide traditional thyristor shapes and compact forms (default `legacy`)
- `gto gate start arrow` / `gto gate end arrow` → arrow tips on GTO thyristor gate leads (default empty)
- `led arrows from anode` / `led arrows from cathode` → emit LED optical arrows from anode or cathode side (default anode)
- `pd arrows to anode` / `pd arrows to cathode` → direct photodiode optical arrows toward anode or cathode (default anode)
- `opto start arrow` / `opto end arrow` → arrow tips on optical device arrows (default empty / standard)
- `opto arrows/color` → stroke color of optical arrows (default component color)
- `opto arrows/relative thickness` → line thickness multiplier for optical arrows (default 1.0)
- `opto arrows/dash` → dash pattern for optical arrows (e.g. `{{1pt}{1pt}}`, default unbroken)
- `diode straight whiskers` / `diode sloped whiskers` → right-angle vs angled whiskers on Zener/TVS diodes (default sloped)
- `batteries/scale` → scale all battery bipoles (default 1.0)
- `sources/scale` → scale all independent sources and generators (default 1.0)
- `csources/scale` → scale all dependent / controlled sources (default 1.0)
- `sources/symbol/thickness` / `csource/symbol/thickness` → relative line thickness of waveform/polarity symbols inside sources
- `sources/symbol/rotate` → rotation angle of internal waveform (default 90 along path; `auto` keeps waveform vertical)
- `bipoles/isourcesin/angle` → opening angle of sinusoidal current source circle arc (default 80)
- `bipoles/dcisource/angle` → opening arc angle of DC current source (0–90, default 80)
- `circuitikz/bipoles/noise sources/fillcolor` → background fill of noise sources; set to `dashed` for fillable pattern
- `bipoles/vsourceam/inner plus` / `.../inner minus` → polarity characters for American voltage sources (default $+$ and $-$)
- `bipoles/vsourceam/margin` → radial margin positioning inner polarity signs in American sources (default 0.7)
- `sources/symbol/sign rotation=default|<deg>|straight|auto` → rotation mode for polarity signs in American voltage sources
- `ootrans size=small|large` → match overlapping transformer source circles to standard single-source radius (default `small`)
- `sources/symbol/xdelta split` → relative size of inner delta triangle in transformer source vector groups (default 0.58)
- `sources/symbol/{delta,wye,eyw,zig} scale` → scale individual three-phase winding vector group symbols (default 1)
- `sources/symbol/{delta,eyw,wye,zig} rot` → rotate individual three-phase winding vector group symbols
- `sources/symbol/{prim,sec,tert,upper} rot` → rotate vector group symbol on specific winding (primary, secondary, tertiary, upper)
- `oo upper winding` → add third top winding circle to `oosourcetrans` / `oosourceatrans` (optional relative size argument)
- `bipoles/oosourcetrans/upper circle size default` → default relative diameter of upper winding circle (default 0.7)
- `bipoles/oosourcetrans/upper circle offset` → vertical center offset of upper winding circle (default 1.3)
- `instruments/scale` → scale all meters, oscilloscopes, and probes (default 1.0)
- `bipoles/currtap/dot size` → relative size of inner contact dot on `currtap` probe (default 0.5)
- `bipoles/smeter/index position` / `bipoles/qmeter/index position` → pointer angle percentage on square meters / probes (0–100, default 80)
- `bipoles/oscope/waveform=ramps|sin|square|triangle|lissajous|zero|none` → display trace on oscilloscope screen (default `ramps`)
- `bipoles/oscope/width` / `bipoles/oscope/height` → dimensions of oscilloscope display screen (default 0.6)
- `rotated instruments` / `straight instruments` → rotate meter face text with wire angle or hold horizontal (default `straight`)
- `mechanicals/scale` → scale all mechanical analogy bipoles (default 1.0)
- `misc/scale` → scale all miscellaneous bipoles (default 1.0)
- `misc/thickness` → line thickness of miscellaneous components relative to base line width
- `bipoles/wfuse/dots=true|false` → show or hide terminal solder dots on wiggly fuse (default `true`)
- `bipoles/wfuse/shape` → pole node shape used for wiggly fuse terminal dots (e.g. `circ`, `osquarepole`)
- `bipole/openbarrier/gap` → relative opening gap of `openbarrier` (default 1.0 = full width)
- `sparkgap/circle` / `sparkgap/dot` → boolean flags adding circular enclosure and gas-filled dot to `sparkgap` (default false)
- `sparkgap/gap` → spark gap separation distance (default 0.05)
- `sparkgap end arrow` → arrow tip specification for spark gap electrodes (default `Triangle[scale=2]`)
- `bipoles/mic/bar thickness` → relative line thickness of straight diaphragm bar on microphones (default 1)
- `dc symbol/width` / `dc symbol/height` → bounding dimensions of `dc symbol` annotation node (defaults 0.3 / 0.15)
- `ac symbol/width` / `ac symbol/height` → bounding dimensions of `ac symbol` annotation node (defaults 0.3 / 0.15)
- `dc symbol/text offset` / `ac symbol/text offset` → distance between symbol and label text above it (default 6pt)
- `dc symbol/segments` → number of dash segments in bottom line of `dc symbol` (default 2; also `dc symbol segments`)
- `dc symbols/relative thickness` → line thickness multiplier for bottom dashed line of `dc symbol` (default 1)
- `bipoles/crossing/size` → scale factor of jumper crossing arch (default 0.2)
- `crossing vertical/relative thickness` → line thickness multiplier for vertical crossing wire (default 1.0 = choke line)
- `crossing vertical/color` / `crossing vertical/dash` → color and dash pattern for vertical crossing line
- `current arrow scale` → size divisor for current and flow arrows (default 16; larger value yields smaller arrow)
- `tunable start arrow` / `tunable end arrow` → arrow tip specifications used by `\ctikztunablearrow` macro
- `nodes width` → diameter of terminal poles (`circ`, `ocirc`, etc.) relative to basic bipole length (default 0.04)
- `open nodes fill` → background fill color for open terminal poles (`ocirc`, `odiamondpole`, `osquarepole`; default `white`)
- `connectors/scale` → scale all connector elements (default 1.0)
- `connectors/fill` → fill color for connector shapes (default none)
- `connectors/thickness` → relative line thickness of connector outlines

## Traps
- **Never use TikZ scaling inside `to[...]`**: `to[R, scale=0.8]` or `to[D, xscale=-1]` corrupts path geometry completely. Use `mirror` to flip orientation across the wire and `invert` to swap electrical terminals.
- **Node text mirroring**: When flipping node-style components (e.g. `node[bnc, xscale=-1]`), text labels are inverted; wrap labels in `\ctikzflipx{$v_o$}` or `\scalebox{-1}[1]{$v_o$}`.
- **Diode forced variants vs global styles**: Bare diode names (`D`, `sD`, `zD`, `Ty`) follow package options (`fulldiode`, `emptydiode`). Always write `Do` / `D*` / `D-` to guarantee reproducible empty, full, or stroke appearance across documents.
- **Tripole control terminal naming**: Thyristors (`Tr`, `Ty`, `GTO`, `IGCT`) and potentiometers (`pR`) require a named bipole (`n=NAME` or `name=NAME`) to wire their control terminal: `(NAME.G)` / `(NAME.gate)` for thyristors, `(NAME.wiper)` / `(NAME.W)` for potentiometers.
- **Shorthand source naming collisions**: Shorthands `V`, `I`, `cV`, and `cI` depend silently on package options `europeanvoltages` / `americanvoltages` and `europeancurrents` / `americancurrents`. Use explicit names (`vsourceEU`, `vsourceAM`, `cvsourceAM`) when exact symbol style must not depend on global flags.
- **Noise sources are gray-filled**: Independent noise sources (`vsourceN`, `isourceN`) have a solid gray interior by default; they reject custom `fill` colors unless `circuitikz/bipoles/noise sources/fillcolor=dashed` is enabled.
- **Nullator and Norator label suppression**: Though members of class `sources`, `nullator` and `norator` internally set `bipoles/is voltage=false` and `bipoles/is current=false`, disabling automated source labeling mechanics.
- **Legacy voltmeter and ammeter rotation defect**: `voltmeter`, `ammeter`, and `ohmmeter` are fixed-horizontal shapes and render incorrectly when placed along vertical or diagonal paths. Use `rmeterwa` or `smeter` with `t=V` / `t=A` (which keep their label text horizontal at any wire angle) or QUCS probes `qvprobe` / `qiprobe`.
- **Transformer source circle shrinkage**: Because overlapping transformer sources (`oosourcetrans`, `oosourceatrans`, `ooosource`) are auto-scaled to fit standard source length, their individual circles appear shrunken. Set `ootrans size=large` or local `sources/scale=1.667` to match single-circle source radius.
- **Path crossing centering trial-and-error**: `to[crossing]` always places the bridge at the midpoint of the TikZ path segment. For precise multi-wire alignment, use standalone `node[jump crossing]` or `node[plain crossing]` and connect through `(X.west)`, `(X.east)`, `(X.north)`, `(X.south)`.
- **Ground orientation and connection point**: Grounds always develop strictly downward from their `center` anchor (`center` coincides with `north` terminal). Connecting grounds to non-horizontal wires requires manual rotation or intermediate coordinate routing.
- **Inductor midtap parity and core alignment**: Inductor `midtap` flips between the top and bottom of the coil depending on whether `inductors/coils` is odd or even; use geographical anchors (`north`/`south`) if a fixed side is needed. Inductor core lines drawn between `core west` and `core east` do not shift label text; adjust label position manually.
- **Zigzag resistor wire joins at large thickness**: American resistors drawn with `thickness > 2` develop unsightly corner spikes where zigzag vertices join leading wires; resolve with `\ctikzset{resistors/zigzag hook/.code={\pgfsetroundcap\pgfsetroundjoin}}` or `\ctikzset{resistors/zigzag stub=0.05}`.
- **Jack Tap tip auto-reversal**: The `Jack Tap` arrow tip (`-v`, `^-`, `-vf`, `-^f`) automatically reverses direction when placed at the start of a path segment.

Cross-checked against manual index: 361 names verified.
