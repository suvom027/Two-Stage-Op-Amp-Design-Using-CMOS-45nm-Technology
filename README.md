# Two-Stage Op-Amp Design Using CMOS 45nm Technology


---

## Table of Contents

- [Abstract](#abstract)
- [Course Information](#course-information)
- [Team Members](#team-members)
- [Design Specifications](#design-specifications)
- [Circuit Architecture](#circuit-architecture)
- [Hand Calculations](#hand-calculations)
- [Transistor Sizing](#transistor-sizing)
- [Simulation Results](#simulation-results)
- [Repository Structure](#repository-structure)
- [How to Reproduce Simulations](#how-to-reproduce-simulations)
- [Conclusion](#conclusion)
- [References](#references)

---

## Abstract

This project presents the design and analysis of a two-stage operational amplifier (Op-amp) implemented using the CMOS 45nm within the Cadence Virtuoso design environment. The circuit comprises a differential amplifier as the first stage and a common-source amplifier as the second stage, powered by a 1.2 V supply with a single bias current source. Comprehensive simulations - including DC, AC, transient, and noise analyses - were performed using the Spectre simulator to validate the design against target performance metrics.

---

## Course Information

| Field | Detail |
|-------|--------|
| Course | Analog Integrated Circuits (EEE 4117 B) |
| Institution | United International University (UIU) |
| Department | Electrical and Electronic Engineering |
| Supervisor | Dr. Md. Hasanuzzaman, Assistant Professor |
| Submission Date | 8th July 2025 |
| EDA Tool | Cadence Virtuoso (Spectre Simulator) |
| Technology | CMOS 45nm (1.2 V devices) |

---

## Team Members

| Name | Student ID |
|------|------------|
| Md. Mahruf Tanzin Elahie | 021 201 001 |
| Azad Hossain Saykat | 021 221 017 |
| Suvom Karmakar | 021 221 027 |
| Md. Tahman Hossain | 021 221 099 |

---

## Design Specifications

| Parameter | Target | Achieved |
|-----------|--------|----------|
| Voltage Gain | 55–70 dB | **55.2 dB** |
| Phase Margin | 60–70° | **70°** |
| Unity-Gain Bandwidth | 50–60 MHz | **54.23 MHz** |
| ICMR⁻ | 0.3–0.6 V | **0.4 V** |
| ICMR⁺ | 0.9–1.0 V | **1.0 V** |
| Slew Rate | 5–20 V/µs | **12 V/µs** |
| Load Capacitance (C_L) | 1–5 pF | **4 pF** |
| Supply Voltage (V_DD) | 1.2 V | **1.2 V** |
| Bias Current | Minimized | **10 µA** |

---

## Circuit Architecture

The op-amp employs a standard two-stage Miller-compensated topology:

```
<p>
   <img width="691" height="519" alt="Image" src="https://github.com/user-attachments/assets/a910d9df-748f-4e16-befa-945e4047ce82" />
</p>


```

| Transistor | Type | Role |
|------------|------|------|
| M1, M2 | NMOS | Differential input pair |
| M3, M4 | PMOS | Current-mirror active load (Stage 1) |
| M5 | NMOS | Tail current source |
| M6 | PMOS | Common-source amplifier (Stage 2) |
| M7 | NMOS | Current-source load (Stage 2) |
| M8 | NMOS | Bias reference transistor |
| Cc | — | Miller compensation capacitor (1.2 pF) |
| CL | — | Output load capacitor (4 pF) |

All transistors biased in **saturation region (region = 2)** as confirmed by DC simulation.

---

## Hand Calculations

### Step 1 — Compensation Capacitor

To achieve phase margin > 60°:

```
Cc ≥ 0.22 × CL = 0.22 × 3 pF = 660 fF ≈ 1 pF
```

### Step 2 — Bias Current

```
ID5 = SR × Cc = 10 V/µs × 1 pF = 10 µA
```
A slightly higher value of **ID5 = 15 µA** was selected for design margin.

### Step 3 — Input Pair Transconductance (M1, M2)

```
gm1,2 = GBW × Cc = 2π × 55×10⁶ × 1×10⁻¹² = 345.5 µS
```

### Step 4 — W/L of M1, M2

Using CMOS 45nm parameters (µnCox ≈ 350 mA/V², |Vth,n| = 400 mV, L = 400 nm):

```
(W/L)1,2 = gm² / (2 × ID × µnCox) = (345.5µ)² / (2 × 7.5µ × 350m) ≈ 22.73
→ W1,2 = 22.73 × 400 nm ≈ 9.09 µm
```

### Step 5 — W/L of M3, M4 (from ICMR+)

```
VSD3 = VDD − VD1 = 1.2 − 0.6 = 0.6 V
(W/L)3,4 = 2×ID / (µpCox × (VSD3 − |Vth,p|)²)
     = 2×7.5µ / (300m × (0.6−0.335)²) ≈ 0.7119
```

### Step 6 — W/L of M5, M8 (from ICMR−)

```
VGS1 = Vth,n + √(2×ID1 / (µnCox×(W/L)1))
   = 0.4 + √(2×7.5µ / (350m×22.73)) ≈ 0.45 V

VD,sat = Vin,cm − VGS1 = 0.4 − 0.45 = −0.05 V

(W/L)5,8 = 2×ID5 / (µnCox × VD,sat²) ≈ 34.28
```

### Step 7 — Second Stage (M6, M7)

```
gm6 = 10 × gm1,2 = 10 × 345.5 µS = 3.455 mS
Rc = 1 / gm6 = 1 / 3.455×10⁻³ ≈ 290 Ω

gm4 = √(2 × ID4 × µpCox × (W/L)4) = 56.59 µS

(W/L)6 = (gm6/gm4) × (W/L)4 = (3.455m / 56.59µ) × 0.7119 ≈ 43.45
ID6 = (W/L)6 / (W/L)4 × ID4 ≈ 457.81 µA

(W/L)7 = (ID7/ID5) × (W/L)5 = (457.81µ / 15µ) × 34.28 ≈ 1046
```

---

## Transistor Sizing

### Theoretical vs. Simulated Dimensions

| Transistor | Type | W (theoretical) | W (simulated) | L (simulated) | W/L (simulated) |
|-----------|------|----------------|---------------|---------------|-----------------|
| M1, M2 | NMOS | 9.09 µm | 10 µm | 400 nm | 25 |
| M3, M4 | PMOS | 285 nm | 910 nm | 385 nm | 2.37 |
| M5 | NMOS | 13.7 µm | 10 µm | 500 nm | 20 |
| M6 | PMOS | 17.38 µm | 37.5 µm | 385 nm | 97.40 |
| M7 | NMOS | 418 µm | 200 µm | 500 nm | 400 |
| M8 | NMOS | 13.7 µm | 10 µm | 500 nm | 20 |


---

## Simulation Results

### DC Operating Point

| Transistor | Region | Bias Current (µA) | gm (µS) |
|-----------|--------|-------------------|---------|
| M1, M2 | Saturation | 13.24 | 248.68 |
| M3, M4 | Saturation | 13.24 | 123.29 |
| M5 | Saturation | 26.49 (×2 mult.) | 480.76 |
| M6 | Saturation | 454.70 | 4357 |
| M7 | Saturation | 454.70 | 7657.5 |
| M8 | Saturation | 14.997 | 266.2 |

Common-mode input bias set to **735 mV** (midpoint of ICMR: 620 mV to 850 mV).

### Gain and Output Impedance

```
Stage 1: Av1 = gm1 × (ro2 ∥ ro4)
       = 248.67 µS × (112.797 kΩ ∥ 279.439 kΩ)
       = 19.98 V/V

Stage 2: Av2 = gm6 × (ro6 ∥ ro7)
       = 4.357 mS × (10.2 kΩ ∥ 15.5 kΩ)
       = 26.8 V/V

Total:  Av = Av1 × Av2 = 535.7 → 20·log₁₀(535.7) ≈ 54.6 dB

Output impedance Stage 1: Zout1 = ro2 ∥ ro4 = 80.36 kΩ
Output impedance Stage 2: Zout2 = ro6 ∥ ro7 = 6.15 kΩ
```

### AC Analysis

| Metric | Value |
|--------|-------|
| Low-frequency gain | 55.20 dB |
| Unity-gain frequency | 54.23 MHz |
| Phase at unity gain | −110° |
| **Phase margin** | **70°** |

Frequency sweep: 1 Hz → 10 GHz.

### Transient Analysis

- **Input:** 2 mV peak-to-peak sinusoid superimposed on 735 mV DC common-mode voltage
- **Output:** Clean sinusoidal waveform, peak ~994 mV, no visible distortion
- **Slew rate:** 12 V/µs (within target of 5–20 V/µs)

### Noise Analysis

- Frequency sweep: 1 Hz → 10 GHz
- Input-referred noise within acceptable limits for the CMOS 45nm process Technology
- Dominant noise contribution: thermal and flicker noise from M1, M2 input pair

---


## Conclusion

The two-stage CMOS op-amp designed in the CMOS 45nm Technology successfully meets all target specifications. The Miller compensation technique with Cc = 1.2 pF yielded a stable phase margin of 70°. Calculated and simulated gain values agree closely (54.6 dB vs. 55.2 dB), validating the hand-calculation methodology. The design demonstrates effective trade-offs between gain, bandwidth, power consumption, and stability in a nanometer-scale CMOS process.

---
