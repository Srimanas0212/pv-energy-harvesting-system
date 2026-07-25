# 6. Result and Analysis

## 6.1 Introduction

This chapter presents simulation results for the **complete integrated energy harvester system**, moving beyond the individual block-level results of earlier chapters to evaluate overall system behavior that includes energy harvesting, control, voltage conversion, and regulation working together.

## 6.2 System-Level Transient Response

Full-system transient behavior was simulated in **Cadence Virtuoso**. Results show the system reaches steady state after an initial transient, with PV voltage remaining nearly constant indicating stable operation. The MPPT controller's output (maximum power voltage and duty cycle) converges smoothly without oscillation, confirming a stable feedback control loop.

## 6.3 MPPT Control Behavior in Integrated System

The duty cycle is produced by analyzing environmental data and PV voltage. Observations:

- The duty cycle fluctuates initially, then settles to a steady value.
- The estimated MPP voltage stays within a bounded limit.
- PV voltage is maintained near a steady operating state.

The designed controller successfully holds a steady operating point at steady state.

## 6.4 VCO and Switching Signal Analysis

The VCO takes the duty cycle as input and produces clock signals for the charge pump. Observations:

- Two complementary clock pulses are produced.
- Output pulses are periodic and stable.
- Signal frequency remains constant after an initial transient period.

The VCO reliably generates the switching signal required by the charge pump.

## 6.5 Charge Pump Output Performance

The charge pump boosts the PV-source voltage. Observations:

- Output voltage is significantly higher than the PV input.
- The boosting process stabilizes after an initial transient.
- Output voltage ripple stays within acceptable limits.

**Result**: an input voltage of ~2 V is boosted to ~3.27 V using complementary clock signals (see `images/fig6.1-integrated-system-waveforms.png`).

## 6.6 Overall System Energy Conversion

Multi-stage energy conversion pipeline:

1. PV cell generates electrical energy.
2. MPPT controller determines control action (duty cycle).
3. VCO produces switching signals.
4. Charge pump boosts voltage.

The cooperative operation of these blocks forms a complete energy harvesting system, with the control and power conversion stages providing stable operation free of instabilities.

## 6.7 Error Amplifiers Performance

Error amplifier performance directly affects LDO regulation accuracy, circuit stability, and transient response. Key parameters: open-loop gain, bandwidth (response speed), phase margin (stability), slew rate (pass-transistor drive capability).

| Parameter | NMOS Error Amp | PMOS Error Amp (BGR) |
|---|---|---|
| Gain | 56.52 dB | 58.91 dB |
| Bandwidth | 6.419 MHz | 3.52 MHz |
| Unity Gain Frequency | 1.12 GHz | 839.33 MHz |
| CMRR | 53.20 dB | 69.77 dB |
| Phase Margin | 15.2° @ 787.87 MHz | 18.2° @ 582.81 MHz |
| Gain Margin | 10.89 dB @ 1.49 GHz | 15.56 dB @ 1.46 GHz |
| Slew Rate | 2.039 V/µs | 1.907 V/µs |
| THD | 0.304 | 0.360 |
| PSRR | -67.73 dB | -73.44 dB |

## 6.8 LDO Transient Behaviour

Transient behavior determines how quickly the LDO reaches a stable output voltage following abrupt changes in load current or other parameters.

The output voltage is digitally selectable via control signals `S1S0`:

| S1 S0 | Output Voltage |
|---|---|
| 00 | 0 V |
| 01 | 1.2 V (measured: 1.2671 V) |
| 10 | 1.8 V (measured: 1.80757 V) |
| 11 | 2.5 V (measured: 2.49924 V) |

See waveform figures:
- `images/fig6.2-transient-s1s0-00.png`
- `images/fig6.3-transient-s1s0-01.png`
- `images/fig6.4-transient-s1s0-10.png`
- `images/fig6.5-transient-s1s0-11.png`

## 6.9 Discussion

Simulation results confirm the proposed architecture is capable of stable operation and energy conversion:

- The MPPT stage controls overall system stability.
- The voltage boost stage (VCO + charge pump) provides the boosting required for downstream processing.
- Despite relatively minor changes in simulated PV voltage, the system demonstrates successful closed-loop operation and interaction across all subsystems.

## 6.10 Summary

This chapter presented system-level simulation results for the integrated energy harvesting architecture, demonstrating stable operation with correctly functioning control signal generation and voltage boosting across the full pipeline.

---

## Conclusion

The project successfully designed and validated an integrated PV energy harvesting system combining ML-based MPPT, charge-pump voltage boosting, and a digitally programmable LDO backed by a Banba-topology BGR. Simulation results confirm stable, closed-loop operation across all integrated subsystems, with the charge pump boosting a ~2 V PV input to ~3.27 V and the LDO providing four selectable, stable output voltage levels (0 V, 1.2 V, 1.8 V, 2.5 V).

## Future Scope

Potential extensions to this work include:

- Improving MPPT convergence speed and accuracy using more sophisticated ML models (e.g., polynomial regression or gradient boosting) while managing hardware complexity trade-offs.
- Exploring higher-efficiency charge pump topologies (e.g., modified Dickson charge pumps) to reduce ripple and improve voltage gain.
- Investigating capacitor-less (capless) LDO variants for further area/cost reduction.
- Extending the programmable LDO to additional output voltage levels or finer-grained digital control.
- Silicon fabrication and hardware validation of the integrated PMU.
