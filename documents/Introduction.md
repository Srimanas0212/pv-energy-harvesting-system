# 1. Introduction

## 1.1 Introduction

Fast growth and innovation in microelectronic devices and computing platforms has driven the need for more efficient power delivery and management. Traditional battery-powered approaches suffer from limited lifetimes and scalability problems, which has made energy harvesting technologies increasingly popular for continuous, sustainable operation. Photovoltaics play an essential role here.

Since the power obtained from photovoltaics depends heavily on light intensity and ambient temperature, efficiently harvesting and regulating that power is critical. This project develops a complete system for power management and harvesting from a photovoltaic cell, including a voltage booster and regulator, implemented as a Power Management Unit (PMU):

- A **Maximum Power Point Tracking (MPPT)** algorithm forms the intelligent front-end, maximizing system performance.
- Energy is further regulated using a **Voltage Controlled Oscillator (VCO)** and **charge pump**.
- A **Low-Dropout Regulator (LDO)** guarantees dependable operation of downstream components regardless of load variation, using a high-gain amplifier, pass transistor, and programmable feedback path for output voltage scaling.
- A **Bandgap Reference (BGR)** block produces a constant voltage regardless of temperature and supply variation.

## 1.2 Literature Survey

Research relevant to this work spans hardware security, energy harvesting, power conversion, and voltage regulation for IoT systems:

- **Security**: Ram et al. proposed a configurable ring-oscillator-based PUF for secure, reliable IC identification in constrained environments [1].
- **MPPT / Energy Harvesting**: Ranjith et al. combined machine learning with Perturb & Observe (P&O) MPPT for improved tracking under dynamic conditions [2]. Devaraj et al. developed a switched-capacitor multi-input harvester integrating solar and piezo sources [3]. Khanam et al. further enhanced MPPT using ML-based adaptive techniques [4].
- **Charge Pumps / Power Conversion**: Zhang et al. introduced a modified Dickson charge pump with higher voltage gain and efficiency [5]. Tabrizi et al. designed an autonomous MPPT-enabled interface for thermoelectric generators [6]. Pinninti et al. proposed a digitally controlled reconfigurable charge pump for adaptive solar harvesting [7].
- **System-Level Designs**: Ram et al. developed Eternal-Thing 2.0, a ripple-less, Trojan-resilient solar harvesting system [8], followed by Eternal-Thing 3.0, a mixed-mode SoC for scalable IoT energy solutions [9].
- **LDO Regulation**: Wu et al. proposed a programmable output LDO for flexible voltage scaling [10]. Milliken et al. demonstrated a fully integrated CMOS LDO with improved stability [11].
- **Ultra-low-power designs**: Rashidian et al. presented a nano-watt BGR with curvature compensation [12]. Abd Aziz et al. developed an efficient LDO in 0.18 µm CMOS [13].
- **BGR design**: Lee et al. introduced a sub-µW circuit with inherent curvature compensation [14]. Alam provided foundational amplifier-based BGR design approaches [15].
- **IoT-focused LDOs**: Kim et al. proposed an ultra-low quiescent current LDO with adaptive modes [16]. Additional low-power, capacitor-less LDO techniques appear in [17–19].
- **Low-voltage BGR evolution**: Banba et al.'s sub-1V CMOS design [20], Pereira's ultra-low-power capless LDO [21], Dalal et al.'s sub-1V BGR implementations [22].
- **Fundamentals**: Day detailed fundamental LDO concepts [23]. Lin and Liang demonstrated subthreshold BGR operation for improved efficiency [24]. Hu et al. proposed a high-performance Brokaw BGR with enhanced stability [25].

## 1.3 Gap Analysis

Current research largely improves individual modules (MPPT, charge pumps, LDOs, bandgap references) in isolation, without addressing the integration of these components into a full energy harvesting system:

- The effect of ML-based MPPT on downstream analog blocks is under-investigated.
- Charge pump research focuses on voltage boost and efficiency, but rarely accounts for ripple and instability effects on the regulation stage.
- Existing LDO architectures optimize dropout/PSRR but largely ignore adaptability under dynamic loading conditions.
- Integration of stable reference circuits in energy-constrained, low-voltage systems is often overlooked.

This project addresses that gap by integrating an MPPT algorithm, charge-pump-based voltage boosting, and an LDO architecture with a stable reference circuit into one system.

## 1.4 Motivation

Autonomous electronic systems for IoT and edge computing demand efficient, practical energy harvesting solutions, since batteries fall short on scalability and operational longevity. However, harvested energy is inherently limited and unreliable, varying with environmental conditions necessitating efficient extraction, boosting, and regulation. Most existing research tackles each step separately; this project aims to ensure compatibility across the full pipeline for an efficient, stable system.

## 1.5 Problem Statement

Solar cells produce low, unpredictable, and unsteady energy, making it difficult to power electronic devices directly. It is therefore important to consider how efficiently energy can be harvested, converted, and regulated from such sources. Developing an efficient energy harvesting system is essential for optimal power extraction and boosting, as well as maintaining stable voltage levels.
