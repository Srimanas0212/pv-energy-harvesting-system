# 3. MPPT and Charge Pump System Design

## 3.1 Introduction

This chapter details the design and implementation of the proposed PV energy harvesting front-end: an ML-based MPPT controller, a Voltage Controlled Oscillator (VCO), and a charge pump. The MPPT controller determines the optimal operating point; the VCO and charge pump handle the switching and voltage boosting.

## 3.2 Overall System Architecture

Main blocks:

1. **Solar PV Cell** : converts light to electrical energy.
2. **MPPT Controller (ML-based)** : takes sensed parameters and outputs a duty cycle.
3. **Voltage Controlled Oscillator (VCO)** : converts the duty cycle into switching clock signals.
4. **Charge Pump (Voltage Doubler)** : uses the VCO clocks to double the output voltage.

## 3.3 Photovoltaic Cell Design

A **Verilog-A single-diode-equivalent model** was developed for the PV cell, capturing:

- Irradiance dependence
- Temperature variation
- Resistive losses
- Current-voltage nonlinearity

The PV cell's voltage and current outputs feed the MPPT control algorithm; PV current is sensed via a small sensing resistor. The model was implemented and validated in the Cadence environment (see ![Photovoltaic Cell Model](..images/3.1-PV.png) and [](..images/3.2-PV_plot.png).

### 3.3.1 Dataset and Model Development

A dataset was generated representing PV behavior across varying irradiance (G) and temperature (T), capturing `Vmp`, `Imp`, `Pmp`, `Voc`, and `Isc`.

| G (W/m²) | T (°C) | Vmp (V) | Imp (A) | Pmp (W) | Voc (V) | Isc (A) |
|---|---|---|---|---|---|---|
| 100 | 10 | 1.7598 | 0.0454 | 0.0799 | 2.09 | 0.0493 |
| 100 | 32.1 | 1.6188 | 0.0460 | 0.0745 | 1.96 | 0.0504 |
| 100 | 54.2 | 1.4836 | 0.0465 | 0.0690 | 1.82 | 0.0515 |
| ... | ... | ... | ... | ... | ... | ... |
| 1000 | 72.6 | 1.35609 | 0.47267 | 0.64098 | 1.7144 | 0.5238 |
| 1000 | 76.3 | 1.33515 | 0.47263 | 0.63103 | 1.6922 | 0.52565 |
| 1000 | 80 | 1.31262 | 0.47313 | 0.62104 | 1.67 | 0.5275 |

*(Full dataset: see `simulation/pv_dataset.csv` — add your source data file here.)*

**Observed trends**: As temperature increases, the MPP voltage decreases; as irradiance increases, current and power output increase which is consistent with practical PV behavior.

The dataset was processed in Python, evaluating multiple regression models to estimate MPP voltage as a function of irradiance and temperature. The best-performing model coefficients were implemented in Verilog-A for circuit-level simulation.

### 3.3.2 Model Performance and Selection

Several ML models were evaluated for MPPT prediction, assessed via R², RMSE, and MAE:

- **Polynomial regression (degree 2)** and **gradient boosting** achieved the highest accuracy with minimal error.
- **Linear regression** was ultimately selected for implementation, due to its simplicity, low computational complexity, and ease of integration into Verilog-A ends up balancing prediction accuracy with hardware feasibility.

## 3.4 Voltage Controlled Oscillator (VCO)

The VCO converts the analog duty-cycle signal from the MPPT controller into switching clock signals for the charge pump. Oscillation frequency scales with the input duty cycle. The VCO generates two complementary clock signals:

- **CLK1**
- **CLK2**

These drive the alternating switching modes of the charge pump.

## 3.5 Charge Pump Design

The charge pump is implemented as a **voltage doubler** circuit to boost the PV source voltage.

### 3.5.1 Operating Principle

Charge is moved between capacitors via switching action, using complementary clock signals for efficient energy transfer (see `images/3.3-Doubler.png`).

### 3.5.2 Circuit Implementation

Composed of:
- Switching transistors
- Capacitors
- Clock-controlled switching signals, driven by the VCO output

### 3.5.3 Output Characteristics

The charge pump output voltage exceeds the PV input voltage and is used to drive low-voltage electronics. Efficiency depends on switching rate, loading, and capacitance.

**Result**: an input voltage of ~2 V is boosted to ~3.27 V using complementary clock signals (see `images/3.4-Doubler_Plot.png`).

## 3.6 Integration of System Components

All blocks are connected into a continuous energy-harvesting pipeline:

1. PV cell - energy source
2. MPPT controller - receives sensed signals, provides duty cycle
3. VCO - converts duty cycle to clock signals
4. Charge pump - increases voltage

This interplay allows the circuit to operate continuously and harvest energy.
