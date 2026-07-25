# 4. Programmable Low Dropout Regulator

## 4.1 Introduction

Modern SoC designs especially battery-constrained IoT devices require dynamic power management. This project uses a **digitally programmable LDO**: instead of a fixed feedback fraction producing a single output voltage, a 2-bit digital input (`S0`, `S1`) selects among multiple output voltage levels plus a power-down mode.

## 4.2 Design Methodology and Block Diagram

A **top-down** methodology was used: starting from output voltage requirements and stability considerations, then designing each block in sequence.

1. A reference voltage (`Vref`) is chosen via the bandgap reference, feeding the error amplifier.
2. The feedback network (resistors `R1`, `R2`, plus programmable `R3`, `R4` with switches `S0`, `S1`) sets the output voltage and defines selectable/programmable modes.
3. The error amplifier compares the feedback voltage to `Vref`, producing a buffered output that drives the pass transistor gate. Buffering isolates the high-gain amplifier stage and supplies sufficient drive current to charge the pass transistor's gate capacitance.
4. The pass transistor is sized for the required load current while minimizing dropout voltage.

See [Schematic diagram of the circuit](../images/4.1-LDO.png) for the full schematic.

```
Vout = Vref · (1 + R1 / R_effective)
```
where `R_effective` depends on the state of `S0` and `S1`.

Using programmable devices (`M0`, `M1`, `S0`, `S1`) provides flexibility in output voltage level and stability under varying load conditions.

## 4.3 Pass Transistor Element

A **PMOS pass transistor** was chosen because its required gate voltage doesn't need to exceed the supply voltage and is well suited to low-dropout operation. The PMOS operates primarily in the saturation region; output voltage is regulated by modulating its gate-source voltage.

```
ID = (1/2) · μp · Cox · (W/L) · (VSG − |VTH|)²
```
where `μp` = hole mobility, `Cox` = oxide capacitance, `W/L` = transistor aspect ratio.

As input voltage rises or falls, the pass transistor adjusts `VDS` to hold the output constant. See [PMOS pass transistor](../images/4.2-Pass_Element.png)

## 4.4 Error Amplifier

The error amplifier compares the feedback voltage to `Vref` and controls the pass PMOS gate.

### 4.4.1 Topology

A **two-stage CMOS operational amplifier** with an NMOS differential input stage and a common-source gain stage (see [Error Amplifier](../images/4.3-Error_Amplifier.png)). The NMOS input stage was chosen for its larger transconductance per unit of bias current. First stage: NMOS differential pair with PMOS current-mirror load.

### 4.4.2 Small Signal Analysis

```
Av = (gm1 · ro1) · (gm2 · ro2)
```
where `gm1`, `gm2` are the transconductances of the input NMOS and second-stage common-source NMOS, and `ro1`, `ro2` are their output resistances.

All transistors must operate in saturation for proper gain and linearity. Both the feedback voltage and the reference voltage must remain within the amplifier's input common-mode range.

**Measured performance** (see [Frequency Response of the Error Amplifier](../images/4.4-NMOS_Gain.png) and [Common-Mode Rejection Ratio](../images/4.5-NMOS_CMRR.png)
):

| Parameter | Value |
|---|---|
| Gain | 56.5146 dB (@ ~12.59 kHz) |
| Phase crossover | -180° @ ~1.58 GHz |
| CMRR | 53.20 dB (peak, low freq) |

## 4.5 Feedback Network

The feedback network senses and scales down the output voltage before comparing it to `Vref` at the error amplifier. A resistor network of four resistors (`R1 R2 R3 R4`) generates the feedback voltage, applied at the amplifier's non-inverting input (reference at the inverting input).

### 4.5.1 Topology

A digitally programmable resistor divider (see [Feedback Resistors](../images/4.6-Feedback.png)
) output voltage `VO` is controlled by varying the resistance of the lower resistor string via `S1`/`S0`.

### 4.5.2 Programming Logic

NMOS switches `M1` (controlled by `S1`) and `M0` (controlled by `S0`) short out `R3` and `R4` respectively when active.

| S1 | S0 | Vout |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | Vref · (1 + R1/(R2+R3)) |
| 1 | 0 | Vref · (1 + R1/(R2+R4)) |
| 1 | 1 | Vref · (1 + R1/R2) |

**Corresponding measured output levels**: `00` → 0 V, `01` → 1.2 V, `10` → 1.8 V, `11` → 2.5 V.

## 4.6 Bandgap Reference

The BGR provides a temperature-independent reference voltage for the LDO. This design uses a **current-mode BGR**: a temperature-independent reference current is generated first, then converted into a reference voltage improving supply rejection and integrating well with current-mode LDO biasing.

The current-mode approach combines:
- **CTAT** current : proportional to a BJT's base-emitter voltage `VBE` (decreases with temperature).
- **PTAT** current : proportional to the difference in base-emitter voltages (`ΔVBE`) between two BJTs.

### 4.6.1 Topology - Banba BGR

A **Banba topology** was used as it's a widely adopted low-voltage BGR design that performs well at limited supply levels while remaining highly temperature-stable. Unlike traditional voltage-summation bandgap circuits, Banba uses a current-based scheme, well suited to CMOS technology and enabling sub-1V operation (see [Banba BGR](../images/4.7-BGR.png)).

The Banba circuit generates a temperature-independent current by summing a CTAT current (from `VBE`) and a PTAT current (from `ΔVBE` between two BJTs at different current densities):

```
Iref = IPTAT + ICTAT = (VT · ln(N)) / R1 + VBE1 / R2
```
where `VT` ≈ 26 mV at room temperature (300 K), `VBE1` is the base-emitter voltage of Q1, and `N` is the BJT area/current-density ratio.

Output voltage is obtained by passing this current through a resistor:

```
VO = Iref · R4 = (R4/R2) · (VBE1 + (R2/R1) · VT · ln(N))
```

### 4.6.2 PMOS Error Amplifier

A two-stage opamp with a **PMOS differential input stage** provides sufficient gain and biasing at low supply voltages (see [BGR Error Amplifier](../images/4.8-PMOS_EA.png)). The first stage uses a PMOS differential pair with an NMOS current-mirror load. PMOS input devices allow the input common-mode voltage to operate close to ground which is appropriate given the BGR's low `VBE`-level signals.

**Measured performance** (see [Frequency Response of BGR Error Amplifier](../images/4.9-PMOS_Gain.png) and 
[Common-Mode Rejection Ratio](../images/4.10-PMOS_CMRR.png)):

| Parameter | Value |
|---|---|
| Gain | 58.9909 dB @ 10.0 kHz |
| CMRR | 69.7783 dB @ ~7.08 kHz |

### 4.6.3 Resistors Ratio

For zero temperature coefficient (`TC`), the total change in reference current over temperature must be zero:

```
R2/R1 = −(∂VBE1/∂T) / (ln(N) · (∂VT/∂T))
```
where `∂VT/∂T` = 0.0862 mV/K, `∂VBE1/∂T` ≈ -1.8 mV/K, `N` = 8.

### 4.6.4 BGR Operation

Temperature independence is achieved by summing the CTAT and PTAT currents. A temperature sweep confirms a stable reference voltage of approximately **800.6 mV** across the swept range (see [Temperature Sweep](../images/4.11-BGR_Final_output.png)).
