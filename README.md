# Anchor Analysis - ACI/Structural Compliance Tool

**[English](#) | [繁體中文](./README.zh-TW.md)**

## Overview

**Anchor Analysis** is a comprehensive web-based structural engineering application designed to analyze and verify concrete anchor connections according to ACI 318 standards. Developed by FengYu Group, this tool performs detailed calculations for both single anchors and anchor groups under combined tension and shear loading conditions.

## Core Features

### Calculation Engine
- **Single Anchor Analysis**: Complete capacity verification for individual anchors
- **Group Anchor Analysis**: Multi-anchor configurations with eccentricity effects
- **Steel Strength Checks**: Tension and shear capacity of anchor steel
- **Concrete Breakout**: Tension and shear failure modes
- **Pullout & Pryout**: Anchorage withdrawal checks
- **Adhesive Bond**: Chemical anchor bonding strength (optional)
- **Side-Face Blowout**: Near-edge failure verification
- **Shear Lug Analysis**: Bearing plate reinforcement capacity
- **Interaction Check**: Combined loading per ACI trilinear criteria

### User Interface
- **Dual Language Support**: English and Traditional Chinese (繁體中文)
- **Real-time Calculations**: Instant verification with pass/fail indicators
- **Interactive Visualization**: 2D layout drawings and 3D interactive models
- **Cloud Synchronization**: Firebase-powered data storage
- **PDF Report Generation**: Professional calculation reports via native browser printing
- **Responsive Design**: Optimized for desktop, tablet, and mobile devices

### Advanced Features
- **Custom Anchor Layouts**: Grid generator or manual coordinate input
- **Seismic Design Mode**: 0.75 reduction factors per ACI 318-19
- **Geometry Warnings**: Real-time validation of minimum spacing and edge distances
- **Anchor Reinforcement**: Optional supplementary reinforcement calculations
- **Material Limits**: Automatic concrete strength capping (700/560 kgf/cm²)

---

## Design Code Compliance

### ACI 318-19 Sections Implemented

| Section | Description | Failure Mode |
|---------|-------------|--------------|
| 17.6.1 | Steel Strength in Tension | Anchor yielding/fracture |
| 17.6.2 | Concrete Breakout in Tension | Concrete cone failure |
| 17.6.3 | Pullout Strength | Anchor withdrawal |
| 17.6.4 | Side-Face Blowout | Shallow embedment near edge |
| 17.6.5 | Adhesive Bond Strength | Chemical anchor bond failure |
| 17.7.1 | Steel Strength in Shear | Anchor shear yielding |
| 17.7.2 | Concrete Breakout in Shear | Concrete wedge failure |
| 17.7.3 | Concrete Pryout | Compression strut failure |
| 17.8 | Interaction of Tensile and Shear Forces | Combined loading |
| 17.11 | Shear Lug (Kicker Plates) | Bearing and breakout |

---

## Calculation Methods

### 1. Steel Strength (Tension)

**Nominal Strength:**
$$N_{sa} = A_{se} \cdot f_{uta}$$

**Design Capacity:**
$$\phi N_{sa} = \phi_{st} \cdot N_{sa}$$

where:
- $A_{se}$ = Effective cross-sectional area (cm²)
- $f_{uta}$ = Minimum tensile strength (≤ 1.9f<sub>ya</sub> and ≤ 8750 kgf/cm²)
- $\phi_{st}$ = 0.75 (ductile), 0.65 (brittle)

**Check:** $T \leq \phi N_{sa}$

---

### 2. Steel Strength (Shear)

**Nominal Strength:**
$$V_{sa} = 0.6 \cdot A_{se} \cdot f_{uta}$$

**Grouted Anchor Reduction:** $V_{sa} \times 0.8$

**Design Capacity:**
$$\phi V_{sa} = \phi_{sv} \cdot V_{sa}$$

where $\phi_{sv}$ = 0.65 (ductile), 0.60 (brittle)

**Check:** $V \leq \phi V_{sa}$

---

### 3. Concrete Tension Breakout (Single Anchor)

**Basic Strength:**
$$N_b = k_c \cdot \lambda_a \cdot \sqrt{f'_c} \cdot h_{ef}^{1.5}$$

**Projected Area:**
$$A_{Nc} = 2 \times (1.5h_{ef}) \times (C_{a1} + S_1 + 1.5h_{ef})$$

**Reference Area:**
$$A_{Nco} = 9h_{ef}^2$$

**Modification Factors:**
- **Edge Distance:** $\psi_{ed,N} = 0.7 + 0.3 \frac{c_{a,min}}{1.5h_{ef}} \leq 1.0$
- **Cracking:** $\psi_{c,N} = 1.0$ (tension)
- **Splitting:** $\psi_{cp,N} = \max\left(\frac{c_{a,min}}{c_{ac}}, \frac{1.5h_{ef}}{c_{ac}}\right)$ if $c_{a,min} < c_{ac}$

**Design Capacity:**
$$\phi N_{cb} = \phi_c \cdot \frac{A_{Nc}}{A_{Nco}} \cdot \psi_{ed,N} \cdot \psi_{c,N} \cdot \psi_{cp,N} \cdot N_b$$

**Seismic Reduction:** Multiply by 0.75 if seismic design

**Check:** $T \leq \phi N_{cb}$

---

### 4. Concrete Tension Breakout (Group)

**Group Projected Area:**
$$A_{Nc,group} = (C_{a2} + S_2 + 1.5h_{ef}) \times (C_{a1} + S_1 + 1.5h_{ef})$$

**Eccentricity Factor:**
$$\psi_{ec,N} = \frac{1}{1 + \frac{2e'_N}{3h_{ef}}}$$

**Design Capacity:**
$$\phi N_{cbg} = \phi_c \cdot \frac{A_{Nc,group}}{A_{Nco}} \cdot \psi_{ec,N} \cdot \psi_{ed,N} \cdot \psi_{c,N} \cdot \psi_{cp,N} \cdot N_b$$

**Check:** $T \leq \phi N_{cbg}$

---

### 5. Concrete Shear Breakout (Single Anchor)

**Basic Shear Strength:**

$$V_{b1} = 1.86 \left(\frac{l_e}{d_a}\right)^{0.2} \sqrt{d_a} \cdot \lambda_a \cdot \sqrt{f'_c} \cdot C_{a1,1}^{1.5}$$

$$V_{b2} = 3.8 \cdot \lambda_a \cdot \sqrt{f'_c} \cdot C_{a1,1}^{1.5}$$

$$V_b = \min(V_{b1}, V_{b2})$$

**Projected Area:**
$$A_{Vc} = 2 \times (1.5C_{a1,1}) \times h_a$$

**Reference Area:**
$$A_{Vco} = 4.5C_{a1,1}^2$$

**Modification Factors:**
- **Edge Distance:** $\psi_{ed,V} = 0.7 + 0.3\frac{C_{a2}}{1.5C_{a1,1}} \leq 1.0$
- **Height:** $\psi_{h,V} = \sqrt{\frac{1.5C_{a1,1}}{h_a}} \geq 1.0$
- **Cracking/Reinforcement:** $\psi_{c,V}$ = 1.0, 1.2, or 1.4

**Design Capacity:**
$$\phi V_{cb} = \phi_c \cdot \frac{A_{Vc}}{A_{Vco}} \cdot \psi_{ed,V} \cdot \psi_{c,V} \cdot \psi_{h,V} \cdot V_b$$

**Parallel Loading:** Multiply capacity by 2.0 with $\psi_{ed,V} = 1.0$

**Check:** $V \leq \phi V_{cb}$

---

### 6. Pullout Strength

**For Headed Anchors:**
$$N_p = 8 \cdot A_{brg} \cdot f'_c$$

**For Hooked Anchors:**
$$N_p = 0.9 \cdot f'_c \cdot e_h \cdot d_a$$

**For Post-Installed (Certified):**
$$N_p = N_{p,certified}$$

**Nominal Strength:**
$$N_{pn} = \psi_{c,P} \cdot N_p$$

where $\psi_{c,P}$ = 1.4 (uncracked), 1.0 (cracked)

**Design Capacity:**
$$\phi N_{pn} = \phi_p \cdot N_{pn}$$

**Seismic Reduction:** Multiply by 0.75

**Check:** $T \leq \phi N_{pn}$

---

### 7. Concrete Pryout

**Nominal Strength:**
$$V_{cp} = k_{cp} \cdot N_{cp}$$

where:
- $k_{cp}$ = 2.0 (for $h_{ef} \geq 6.35$ cm), 1.0 otherwise
- $N_{cp}$ = Lesser of concrete breakout or adhesive bond strength

**Design Capacity:**
$$\phi V_{cp} = \phi_p \cdot V_{cp}$$

**Check:** $V \leq \phi V_{cp}$

---

### 8. Adhesive Bond Strength (Optional)

**Basic Bond Strength:**
$$N_{ba} = \lambda_a \cdot \tau \cdot \pi \cdot d_a \cdot h_{ef}$$

**Critical Spacing:**
$$c_{Na} = 10d_a\sqrt{\frac{\tau}{77}}$$

**Projected Area:**
$$A_{Na} = (c_{Na} + \min(c_{Na}, C_{a1})) \times (c_{Na} + \min(c_{Na}, C_{a2}))$$

**Reference Area:**
$$A_{Nao} = (2c_{Na})^2$$

**Modification Factors:**
- **Edge Distance:** $\psi_{ed,Na} = 0.7 + 0.3\frac{c_{a,min}}{c_{Na}} \leq 1.0$
- **Splitting:** $\psi_{cp,Na} = \frac{c_{a,min}}{c_{ac}}$ if $c_{a,min} < c_{ac}$

**Nominal Strength:**
$$N_a = \frac{A_{Na}}{A_{Nao}} \cdot \psi_{ed,Na} \cdot \psi_{cp,Na} \cdot N_{ba}$$

**Design Capacity:**
$$\phi N_a = \phi_{bond} \cdot N_a$$

**Seismic Reduction:** 0.8 (cracked) or 0.4 (uncracked)

**Sustained Load Check:**
$$T_{sust} \leq 0.4 \phi N_a$$

**Check:** $T \leq \phi N_a$

---

### 9. Side-Face Blowout

**Applicable When:** Headed anchors with $h_{ef} > 2.5C_{a1}$

**Nominal Strength:**
$$N_{sb} = 42.4 \cdot C_{a1} \cdot \sqrt{A_{brg}} \cdot \lambda_a \cdot \sqrt{f'_c}$$

**Spacing Modification:**
If $S_2 < 6C_{a1}$:
$$N_{sb} = N_{sb} \left(1 + \frac{S_2}{6C_{a1}}\right)$$

**Design Capacity:**
$$\phi N_{sb} = \phi_c \cdot N_{sb}$$

**Check:** $T \leq \phi N_{sb}$

---

### 10. Shear Lug Analysis (Section 17.11)

**Bearing Strength:**
$$V_{brg} = 0.65 \times 1.7 \times f'_c \times A_{ef,sl}$$

where $A_{ef,sl}$ = Effective bearing area

**Concrete Breakout:**
$$A_{Vc,sl} = (3c_{sl} + W_{sl}) \times h_a$$

$$A_{Vco,sl} = 4.5c_{sl}^2$$

$$V_{b,sl} = 3.8 \lambda_a \sqrt{f'_c} \cdot c_{sl}^{1.5}$$

$$\phi V_{cb,sl} = \phi_c \cdot \frac{A_{Vc,sl}}{A_{Vco,sl}} \cdot V_{b,sl}$$

**Design Capacity:**
$$\phi V_{sl} = \min(\phi V_{brg}, \phi V_{cb,sl})$$

**Check:** $V \leq \phi V_{sl}$

---

### 11. Interaction Check (ACI 318-19 Section 17.8)

**Trilinear Interaction:**

1. If $\frac{T}{\phi N_n} \leq 0.2$: No shear reduction required
2. If $\frac{V}{\phi V_n} \leq 0.2$: No tension reduction required
3. Otherwise:

$$\frac{T}{\phi N_n} + \frac{V}{\phi V_n} \leq 1.2$$

where $\phi N_n$ and $\phi V_n$ are the governing (minimum) capacities from all failure modes.

---

## Strength Reduction Factors (φ)

### Steel Elements
| Condition | Tension (φ) | Shear (φ) |
|-----------|-------------|-----------|
| Ductile Steel | 0.75 | 0.65 |
| Brittle Steel | 0.65 | 0.60 |

### Concrete Elements
| Anchor Type | Condition | Category | φ Value |
|-------------|-----------|----------|---------|
| Cast-in | A (Reinforced) | - | 0.75 |
| Cast-in | B (Unreinforced) | - | 0.70 |
| Post-installed | A | 1 (High Reliability) | 0.75 |
| Post-installed | A | 2 (Medium) | 0.65 |
| Post-installed | A | 3 (Low) | 0.55 |
| Post-installed | B | 1 | 0.65 |
| Post-installed | B | 2 | 0.55 |
| Post-installed | B | 3 | 0.45 |

### Seismic Design
- **Concrete/Steel:** 0.75 × base capacity
- **Pullout/Pryout:** 0.75 × base capacity
- **Adhesive Bond:** 0.8 (cracked) or 0.4 (uncracked) × base capacity

### Supplementary Reinforcement
- **Anchor Reinforcement:** φ = 0.75 (replaces concrete breakout when provided)

---

## Material Limits

### Concrete Strength Caps
- **Cast-in Anchors:** $f'_c \leq 700$ kgf/cm² (ACI 318-19 limit)
- **Post-installed/Screw Anchors:** $f'_c \leq 560$ kgf/cm²

### Minimum Geometry Requirements
- **Anchor Spacing:** $S \geq 6d_a$
- **Edge Distance:** $C_a \geq 6d_a$
- **Embedment:** $h_{ef} \geq$ manufacturer recommendations

### Seismic Detailing (Ductile Anchors)
- **Stretch Length:** $L_{str} \geq 8d_a$ (ACI 318-19 17.10.5.3)

---

## Technology Stack

- **Frontend:** HTML5, Tailwind CSS 3.x, JavaScript ES6+
- **3D Graphics:** Three.js r128 with OrbitControls
- **Backend:** Firebase 9.22.0 (Authentication + Firestore)
- **Icons:** Font Awesome 6.4.0
- **PDF Export:** Native browser print API with custom CSS

---

## Units Convention

| Parameter | Unit | Example |
|-----------|------|---------|
| Force | kgf | T = 1000 kgf |
| Length | cm | h<sub>ef</sub> = 8.5 cm |
| Area | cm² | A<sub>se</sub> = 1.57 cm² |
| Stress | kgf/cm² | f'<sub>c</sub> = 280 kgf/cm² |

---

## Safety Notes

⚠️ **Critical Considerations:**

1. **Professional Review Required**: All calculations must be verified by licensed professional engineers
2. **Local Code Compliance**: User is responsible for ensuring compliance with applicable building codes
3. **Conservative Assumptions**: Tool applies ACI 318-19 conservative provisions
4. **Material Properties**: Verify actual material properties with manufacturer data
5. **Installation Quality**: Calculations assume proper installation per manufacturer specifications
6. **Dynamic Loading**: Static load calculations only; dynamic/fatigue analysis not included

---

## Browser Compatibility

- Chrome/Edge (v90+)
- Firefox (v88+)
- Safari (v14+)
- Mobile: iOS Safari, Chrome Mobile

---

## References

- **ACI 318-19**: Building Code Requirements for Structural Concrete
- **ACI 355.2-19**: Qualification of Post-Installed Mechanical Anchors in Concrete
- **ACI 355.4-19**: Qualification of Post-Installed Adhesive Anchors in Concrete

---

## Support

**Email:** gacchuguts@gmail.com

---

**Disclaimer**: This software is provided as a calculation aid for professional engineers. The user assumes all responsibility for the accuracy and appropriateness of input data and the interpretation of results. Always verify calculations independently and ensure compliance with applicable building codes and standards.
