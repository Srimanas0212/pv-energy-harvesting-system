# 2. Background and Theoretical Foundations

## 2.1 Introduction

Photovoltaic (PV) systems are effective, robust energy harvesters that convert solar energy directly to electricity, widely used in IoT applications, wireless sensor nodes, and portable electronics. However, PV systems present two key challenges:

1. **Nonlinear I-V behavior**: PV cell current and voltage depend nonlinearly on solar irradiance and temperature. The output power reaches a maximum at a specific operating point called the **Maximum Power Point (MPP)** which shifts continuously with environmental conditions, requiring active tracking.
2. **Insufficient output voltage**: The raw PV output voltage is typically too low for direct use, requiring a boost/regulation stage. This project uses a charge-pump-based DC-DC converter driven by an ML-based MPPT algorithm.

An **LDO (Low Dropout Regulator)** is a key block in the system, producing a constant, noise-free output voltage regardless of input/load fluctuations, using a feedback loop consisting of a pass transistor, error amplifier, and feedback network.

A **Bandgap Reference (BGR)** generates a precise reference voltage nearly independent of temperature and supply variation, by combining two voltages with opposite temperature coefficients. The BGR supplies the reference voltage to the LDO which regulates the input voltage, together they form a complete voltage regulator.

## 2.2 Photovoltaic Cell Fundamentals

PV cells are semiconductor devices that convert light to electricity via the photovoltaic effect, using a p-n junction that generates electron-hole pairs upon illumination.

### 2.2.1 I–V Characteristics

```
I = Iph − I0 [exp((V + I·Rs) / (n·Vt)) − 1] − (V + I·Rs) / Rsh
```

Composed of three elements:
- **Photocurrent (Iph)** : from incident solar irradiance
- **Diode current** : recombination losses at the p-n junction
- **Shunt current** : leakage current due to defects

### 2.2.2 Thermal Voltage

```
Vt = kT / q
```

Rising temperature raises thermal voltage, influencing diode current and lowering the PV cell's total output voltage.

### 2.2.3 Power Characteristics and Maximum Power Point

```
P = V · I
```

The P-V curve has a unique peak (MPP), where `dP/dV = 0`. Operating at MPP ensures maximum energy extraction.

### 2.2.4 Effect of Irradiance and Temperature

- **Irradiance (G)**: directly proportional to photocurrent. Higher irradiance increases output current and power.
- **Temperature (T)**: increases diode conduction, reduces open-circuit voltage, and slightly increases current.

Because MPP shifts continuously with these conditions, static operation is inefficient.

## 2.3 PV Cell Modeling

The most common equivalent circuit is the **single-diode model**, balancing accuracy and complexity. It comprises:

- A current source for the photocurrent
- A diode representing p-n junction properties
- Series resistance (`Rs`) internal PV cell resistance
- Shunt resistance (`Rsh`)

## 2.4 Maximum Power Point Tracking (MPPT)

MPPT dynamically adjusts the operating point of a PV system (via duty cycle control of a power converter) to track the MPP as environmental conditions change.

**Perturb and Observe (P&O)**: periodically perturbs the operating voltage and observes the resulting power change to determine adjustment direction. Simple, but suffers from oscillation near the peak and slow response to sudden environmental changes.

**Machine-Learning-based MPPT**: instead of continuously searching, an ML model predicts the optimal operating voltage directly from environmental data (irradiance, temperature) avoiding the convergence/oscillation drawbacks of conventional algorithms.

## 2.5 DC-DC Conversion and Charge Pump Fundamentals

PV output voltage is usually insufficient to directly drive electronic circuits, requiring a boost stage. This project uses a **charge pump** which is a DC-DC converter that avoids bulky, costly inductors by relying solely on capacitors reconfigured via clocked switching.

The charge pump works through repeated charge/discharge cycles, transferring energy via capacitors to achieve voltage boosting. For a voltage doubler, the theoretical output is 2× the input, though practical output falls short of doubling.

In this system, the charge pump performs voltage boosting downstream of the MPPT controller; its switching action also determines the effective load presented back to the PV cell, meaning the MPPT controller indirectly governs the PV operating point through the pump's switching behavior.

## 2.6 LDO Fundamentals

An LDO is a linear regulator that produces a stable output voltage with a small input-output voltage differential — ideal for low-power systems, with low output ripple and fast response.

```
Vdropout = Vin - Vout          (typically 30 mV - 300 mV)
```

Main components: pass transistor, error amplifier, feedback network, and reference voltage generator (typically a Bandgap Reference).

```
Vout = Vref · (1 + R1/R2)
```

### 2.6.1 Pass Element
Controls current flow from input supply to output, acting as a voltage-controlled current source whose gate voltage is adjusted by the error amplifier. Sizing affects dropout voltage, load regulation, and efficiency.

### 2.6.2 Error Amplifier
Compares the divided output voltage to the reference voltage, producing an amplified error signal that drives the pass device. High gain is essential to minimize regulation error.

### 2.6.3 Feedback Network
A resistive voltage divider that senses the output voltage and feeds a proportional signal back to the error amplifier, closing the loop. The resistance ratio sets the regulated output voltage.

## 2.7 Bandgap Reference Fundamentals

A BGR generates a constant voltage reference independent of temperature variation, by combining two voltage generators with opposite temperature coefficients:

- **CTAT** (Complementary To Absolute Temperature) : e.g., the base-emitter voltage `Vbe` of a BJT, ranging ~0.6-0.7 V at room temperature, decreasing with rising temperature.
- **PTAT** (Proportional To Absolute Temperature) : e.g., `ΔVbe`, the differential base-emitter voltage between two BJTs operating at different current densities.

Traditional bandgap references target ~1.2 V, dependent on intrinsic silicon properties.
