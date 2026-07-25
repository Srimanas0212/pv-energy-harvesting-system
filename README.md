# Integrated Photovoltaic Energy Harvesting System with Charge Pump Conversion and LDO Regulation

A capstone project presenting the design and simulation of a complete Power Management Unit (PMU) that harvests energy from a photovoltaic (PV) source, boosts the voltage using an ML-assisted MPPT controller and charge pump, and delivers a stable, digitally programmable output using a Low Dropout Regulator (LDO) backed by a Bandgap Reference (BGR) circuit.

## Authors

- Dhanesh Kumar Jallepalli
- Veerisetty Srimanas Chakravarthi

## Abstract

With the rapid growth of low-power microelectronics and self-sustaining edge devices, efficient power management has become essential. This project designs and evaluates a full-fledged PMU that harvests power using photovoltaics and provides regulated voltages. The design uses an intelligent harvesting front-end employing a machine-learning-based MPPT controller to extract maximum power from the PV cell depending on ambient conditions. The harvested power is then processed via a Voltage Controlled Oscillator (VCO) and a charge pump to boost the voltage. A digitally programmable LDO — comprising a pass transistor, high-gain error amplifier, and programmable feedback network — provides a stable, scalable supply voltage for dynamic loads. A Banba-topology Bandgap Reference (BGR), built from a PMOS error amplifier and precisely ratioed resistors, supplies a temperature- and supply-independent reference voltage to the LDO.

**Keywords:** Charge Pump, Voltage Controlled Oscillator, MPPT, Low Dropout Regulator, Bandgap Reference

## System Overview

```
PV Cell → MPPT Controller (ML) → VCO → Charge Pump → BGR → Programmable LDO → Regulated Output
```

- **PV Cell**: Verilog-A single-diode model, characterized against irradiance/temperature dataset.
- **MPPT Controller**: Linear-regression ML model predicting the maximum power point voltage from irradiance and temperature.
- **VCO**: Converts the MPPT duty cycle into complementary switching clocks (CLK1, CLK2).
- **Charge Pump**: Capacitor-based voltage doubler driven by the VCO clocks that boosts ~2 V input to ~3.27 V.
- **BGR (Banba topology)**: Current-mode bandgap reference combining PTAT and CTAT currents for a temperature-stable ~800 mV reference.
- **Programmable LDO**: PMOS pass-transistor regulator with a two-stage NMOS-input error amplifier and a digitally selectable feedback network (2-bit control, S1S0), producing 0 V / 1.2 V / 1.8 V / 2.5 V output modes.

## Key Results

- Charge pump boosts ~2 V PV output to ~3.27 V.
- LDO output modes (S1S0): `00` → 0 V, `01` → 1.2 V, `10` → 1.8 V, `11` → 2.5 V.
- Full system shows stable closed-loop transient behavior with no observed oscillations at steady state.

## Tools Used

- Cadence Virtuoso (schematic capture and transistor-level simulation)
- Verilog-A (PV cell and MPPT behavioral models)
- Python (dataset generation, regression model training/selection)
