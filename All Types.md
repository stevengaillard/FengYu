# Concrete Anchor Analysis (Chapter 17 Compliance)

**[English](#) | [繁體中文](./All%20Types.zh-TW.md)**

**[Simplified](./README.md) | [簡化版](./README.zh-TW.md)**

This documentation outlines the structural analysis logic for anchors in concrete structures, complying with Chapter 17 of the provided code. It covers classification, input parameters, detailed design equations, and output validation.

---

## 1. Anchor Types Supported

The system supports two primary categories of anchors as defined in **Section 17.1.2**:

### A. Cast-in Anchors (Pre-installed)
* **Headed Bolts & Studs:** Anchors with a specific head geometry (ASME B1.1) with pullout strength ≥ $1.4N_P$
* **Hooked Bolts (J- or L-bolts):** Anchors relying on a mechanical hook for pullout resistance ≥ $1.4N_P$ without friction
* **Shear Lugs:** Steel plates welded to base plates to resist high shear loads (Section 17.11)

### B. Post-installed Anchors (Installed after concrete hardening)
* **Expansion Anchors:** Torque-controlled or displacement-controlled, must meet mechanical anchor qualification criteria
* **Undercut Anchors:** Mechanical interlock at the bottom of the hole, must meet mechanical anchor qualification criteria
* **Adhesive (Bonded) Anchors:** Chemical bond using threaded rods or rebar, must meet adhesive anchor qualification criteria
* **Screw Anchors:** Threaded anchors screwed into drilled holes, must meet mechanical anchor qualification criteria

**Important Restrictions:**
- Post-installed mechanical anchors **cannot be reused** after removal (Sec 17.1.3)
- Not applicable for high-cycle fatigue or impact loading (Sec 17.1.4)
- Not applicable for powder-actuated fasteners, through-bolts, or grouted anchors (Sec 17.1.5)

---

## 2. Input Parameters

The following inputs are required to perform the structural calculations.

### 2.1 Geometric & Material Properties

| Parameter | Symbol | Description | Units | Limits/Notes |
|:----------|:-------|:------------|:------|:-------------|
| **Concrete Strength** | $f'_c$ | Specified compressive strength | $kgf/cm^2$ or $MPa$ | Cast-in: ≤ 700 kgf/cm² [70 MPa]<br>Post-installed: ≤ 560 kgf/cm² [55 MPa] |
| **Anchor Diameter** | $d_a$ | Nominal anchor diameter | $cm$ | ≤ 10 cm for concrete breakout calculations |
| **Embedment Depth** | $h_{ef}$ | Effective embedment depth | $cm$ | Screw anchors: $5d_a \le h_{ef} \le 10d_a$ and $h_{ef} \ge 4$ cm<br>Adhesive: $4d_a \le h_{ef} \le 20d_a$ |
| **Tensile Strength** | $f_{uta}$ | Specified tensile strength of anchor steel | $kgf/cm^2$ | ≤ min($1.9f_{ya}$, 8750 kgf/cm²) |
| **Yield Strength** | $f_{ya}$ | Specified yield strength of anchor steel | $kgf/cm^2$ | For ductility checks |
| **Effective Area (Tension)** | $A_{se,N}$ | Effective cross-sectional area (tension) | $cm^2$ | For threaded: $A_{se,N} = \frac{\pi}{4}[0.9743(d_a - \frac{1}{n_t})]^2$ |
| **Effective Area (Shear)** | $A_{se,V}$ | Effective cross-sectional area (shear) | $cm^2$ | Same as $A_{se,N}$ unless specified |

### 2.2 Layout & Spacing

| Parameter | Symbol | Description | Units | Notes |
|:----------|:-------|:------------|:------|:------|
| **Edge Distance 1** | $c_{a1}$ | Distance to nearest edge (shear direction) | $cm$ | Critical for breakout |
| **Edge Distance 2** | $c_{a2}$ | Distance to edge perpendicular to $c_{a1}$ | $cm$ | For edge effects |
| **Anchor Spacing** | $s$ | Center-to-center spacing between anchors | $cm$ | Consider group effects if $s < 3h_{ef}$ |
| **Member Thickness** | $h_a$ | Thickness of concrete member | $cm$ | Affects breakout geometry |

### 2.3 Loads

| Parameter | Symbol | Description | Units | Notes |
|:----------|:-------|:------------|:------|:------|
| **Factored Tension** | $N_{ua}$ | Factored tensile load on anchor/group | $kgf$ | From load combinations |
| **Factored Shear** | $V_{ua}$ | Factored shear load on anchor/group | $kgf$ | From load combinations |
| **Sustained Tension** | $N_{ua,s}$ | Factored sustained tension (adhesive only) | $kgf$ | Long-term loading |

### 2.4 Modification Factors

| Parameter | Symbol | Description | Default Value | Notes |
|:----------|:-------|:------------|:--------------|:------|
| **Lightweight Concrete** | $\lambda_a$ | Lightweight concrete modifier | 1.0 (normal weight) | See Table 17.2.4.1 |
| **Concrete Breakout Coefficient** | $k_c$ | Basic breakout strength coefficient | 10 (cast-in)<br>7 (post-installed) | Up to 10 allowed per evaluation |
| **Critical Edge Distance** | $c_{ac}$ | Edge distance for splitting | Per Table 17.9.5 | Post-installed only |

### 2.5 Condition Factors

| Condition | Parameter | Options | Impact |
|:----------|:----------|:--------|:-------|
| **Concrete Cracking** | — | Cracked / Uncracked at service load | Affects $\psi_{c,N}$, $\psi_{c,V}$, $\psi_{c,P}$ |
| **Supplementary Reinforcement** | — | Yes / No | Affects $\phi$ values and $\psi$ factors |
| **Seismic Design** | — | Yes / No | Applies 0.75 reduction + ductility checks |
| **Installation Direction** (adhesive) | — | Overhead / Upward / Horizontal | Requires special qualification |
| **Post-installed Category** | — | Category 1 / 2 / 3 | Affects $\phi$ per sensitivity/reliability |

---

## 3. Design Strength Calculations ($\phi S_n \ge U$)

### 3.1 Strength Reduction Factors ($\phi$) [Sec 17.5.3]

#### Table 3.1a: Steel-Controlled Failure Modes

| Steel Type | Tension ($\phi$) | Shear ($\phi$) |
|:-----------|:-----------------|:---------------|
| **Ductile** ($f_{uta}/f_{ya} \ge 1.3$, elongation ≥ 14%) | 0.75 | 0.65 |
| **Brittle** | 0.65 | 0.60 |

#### Table 3.1b: Concrete-Controlled (Breakout, Bond, Blowout)

| Reinforcement | Anchor Type | Category | Tension ($\phi$) | Shear ($\phi$) |
|:--------------|:------------|:---------|:-----------------|:---------------|
| **With supplementary reinforcement** | Cast-in | — | 0.75 | 0.75 |
| | Post-installed | 1 | 0.75 | 0.75 |
| | | 2 | 0.65 | 0.65 |
| | | 3 | 0.55 | 0.55 |
| **Without supplementary reinforcement** | Cast-in | — | 0.70 | 0.70 |
| | Post-installed | 1 | 0.65 | 0.65 |
| | | 2 | 0.55 | 0.55 |
| | | 3 | 0.45 | 0.45 |

#### Table 3.1c: Pullout and Pryout

| Anchor Type | Category | Tension (Pullout) | Shear (Pryout) |
|:------------|:---------|:------------------|:---------------|
| Cast-in | — | 0.70 | 0.70 |
| Post-installed | 1 | 0.65 | 0.65 |
| | 2 | 0.55 | 0.55 |
| | 3 | 0.45 | 0.45 |

**Note:** For anchor reinforcement: $\phi = 0.75$ (meets Chapter 25 development requirements)

---

## 4. Tensile Strength Design [Sec 17.6]

All applicable failure modes must be checked. Design strength = **minimum** of all modes.

### 4.1 Steel Strength in Tension ($N_{sa}$) [Sec 17.6.1]

**Single Anchor:**
$$N_{sa} = A_{se,N} \times f_{uta}$$

Where:
- $f_{uta} \le \min(1.9f_{ya}, 8750 \text{ kgf/cm}^2)$

**Check:** $\phi N_{sa} \ge N_{ua}$ for **each anchor** in a group

---

### 4.2 Concrete Breakout Strength ($N_{cb}$ or $N_{cbg}$) [Sec 17.6.2]

#### 4.2.1 Single Anchor

$$N_{cb} = \frac{A_{Nc}}{A_{Nco}} \times \psi_{ed,N} \times \psi_{c,N} \times \psi_{cp,N} \times N_b$$

#### 4.2.2 Anchor Group

$$N_{cbg} = \frac{A_{Nc}}{A_{Nco}} \times \psi_{ec,N} \times \psi_{ed,N} \times \psi_{c,N} \times \psi_{cp,N} \times N_b$$

#### 4.2.3 Basic Concrete Breakout Strength ($N_b$)

**Standard Embedment** ($h_{ef} < 28$ cm):
$$N_b = k_c \times \lambda_a \times \sqrt{f'_c} \times h_{ef}^{1.5}$$

**Deep Embedment** (28 cm ≤ $h_{ef}$ ≤ 63.5 cm, cast-in headed only):
$$N_b = 5.8 \lambda_a \sqrt{f'_c} \times h_{ef}^{5/3}$$
$$N_b = 3.9 \lambda_a \sqrt{f'_c} \times h_{ef}^{5/3} \text{ [SI units]}$$

Where:
- $k_c = 10$ (cast-in), $k_c = 7$ (post-installed)
- Up to $k_c = 10$ allowed for post-installed with qualification testing

#### 4.2.4 Projected Area Calculations

**Reference Area (Single Anchor):**
$$A_{Nco} = 9h_{ef}^2$$

**Actual Projected Area ($A_{Nc}$):**
- Project outward $1.5h_{ef}$ from anchor centerline(s)
- For anchor groups: project from critical row of anchors
- Limited by: $A_{Nc} \le n \times A_{Nco}$ (where $n$ = number of tensioned anchors)
- Reduced by overlapping cones or edges

**Special Case - Thin Members:** When 3+ edges have $c_a < 1.5h_{ef}$:
$$h'_{ef} = \max\left(\frac{c_{a,max}}{1.5}, \frac{s_{max}}{3}\right)$$
Use $h'_{ef}$ in place of $h_{ef}$ for $A_{Nc}$, $A_{Nco}$, and $\psi$ calculations.

#### 4.2.5 Modification Factors ($\psi$)

**Eccentricity Factor ($\psi_{ec,N}$):** [Group only]
$$\psi_{ec,N} = \frac{1}{1 + \frac{e'_N}{1.5h_{ef}}} \le 1.0$$

Where $e'_N$ = eccentricity of resultant tension relative to centroid of tensioned anchors

**Edge Effect Factor ($\psi_{ed,N}$):**
- If $c_{a,min} \ge 1.5h_{ef}$: $\psi_{ed,N} = 1.0$
- If $c_{a,min} < 1.5h_{ef}$: $\psi_{ed,N} = 0.7 + 0.3\frac{c_{a,min}}{1.5h_{ef}}$

**Cracking Factor ($\psi_{c,N}$):**

| Condition | Cast-in | Post-installed ($k_c = 7$) | Post-installed (qualified $k_c$) |
|:----------|:--------|:---------------------------|:----------------------------------|
| **Uncracked at service** | 1.25 | 1.4 | Per evaluation report |
| **Cracked at service** | 1.0 | 1.0 | 1.0 |

**Splitting Factor ($\psi_{cp,N}$):** [Post-installed in uncracked concrete without reinforcement]
- If $c_{a,min} \ge c_{ac}$: $\psi_{cp,N} = 1.0$
- If $c_{a,min} < c_{ac}$: $\psi_{cp,N} = \frac{c_{a,min}}{c_{ac}} + 0.3\frac{h_{ef}}{c_{ac}}$
- Otherwise: $\psi_{cp,N} = 1.0$

---

### 4.3 Pullout Strength ($N_{pn}$) [Sec 17.6.3]

*Applicable to cast-in, expansion, undercut, and screw anchors*

$$N_{pn} = \psi_{c,P} \times N_p$$

#### 4.3.1 Basic Pullout Strength ($N_p$)

**Headed Studs/Bolts:**
$$N_p = 8 \times A_{brg} \times f'_c$$

Where $A_{brg}$ = net bearing area of anchor head

**Hooked Bolts (J or L):**
$$N_p = 0.9 \times f'_c \times e_h \times d_a$$

Where $e_h$ = hook extension length, limited to: $3d_a \le e_h \le 4.5d_a$

**Post-installed (Expansion, Undercut, Screw):**
$N_p$ must be determined by testing per qualification criteria (5% fractile).

#### 4.3.2 Cracking Factor ($\psi_{c,P}$)

| Condition | $\psi_{c,P}$ |
|:----------|:-------------|
| **Uncracked at service** | 1.4 |
| **Cracked at service** | 1.0 |

**Check:** $\phi N_{pn} \ge N_{ua}$ for **each anchor** individually

---

### 4.4 Side-Face Blowout ($N_{sb}$ or $N_{sbg}$) [Sec 17.6.4]

*Applicable to deep headed anchors near edges when $h_{ef} > 2.5c_{a1}$*

#### 4.4.1 Single Anchor

$$N_{sb} = 42.4 \times c_{a1} \times \sqrt{A_{brg}} \times \lambda_a \times \sqrt{f'_c}$$
$$N_{sb} = 13 \times c_{a1} \times \sqrt{A_{brg}} \times \lambda_a \times \sqrt{f'_c} \text{ [SI]}$$

**Modifier for narrow edge:**
If $c_{a2} < 3c_{a1}$: Multiply $N_{sb}$ by $\frac{1 + c_{a2}/c_{a1}}{4}$ where $1.0 \le c_{a2}/c_{a1} \le 3.0$

#### 4.4.2 Anchor Group

When $s < 6c_{a1}$ (parallel to edge):
$$N_{sbg} = N_{sb} \times \left(1 + \frac{s}{6c_{a1}}\right)$$

Where $s$ = spacing of outermost anchors parallel to edge, and $N_{sb}$ is calculated without the $c_{a2}$ modifier.

---

### 4.5 Bond Strength - Adhesive Anchors Only ($N_a$ or $N_{ag}$) [Sec 17.6.5]

#### 4.5.1 Single Adhesive Anchor

$$N_a = \frac{A_{Na}}{A_{Nao}} \times \psi_{ed,Na} \times \psi_{cp,Na} \times N_{ba}$$

#### 4.5.2 Adhesive Anchor Group

$$N_{ag} = \frac{A_{Na}}{A_{Nao}} \times \psi_{ec,Na} \times \psi_{ed,Na} \times \psi_{cp,Na} \times N_{ba}$$

#### 4.5.3 Basic Bond Strength ($N_{ba}$)

$$N_{ba} = \lambda_a \times \tau_{cr} \times \pi \times d_a \times h_{ef}$$

Where:
- $\tau_{cr}$ = characteristic bond stress (cracked concrete), determined by testing per adhesive qualification criteria (5% fractile)
- $\tau_{uncr}$ = characteristic bond stress (uncracked concrete), use when concrete proven uncracked at service

**Default Minimum Values (Table 17.6.5.2.5):** When no product-specific data available

| Installation/Use | Concrete Condition | Max Temp (°C) | $\tau_{cr}$ [kgf/cm²] | $\tau_{uncr}$ [kgf/cm²] |
|:-----------------|:-------------------|:--------------|:----------------------|:------------------------|
| **Outdoor** | Dry to saturated | 79 | 14 [1.4 MPa] | 45 [4.5 MPa] |
| **Indoor** | Dry | 43 | 21 [2.1 MPa] | 70 [7.0 MPa] |

*Multiply by 0.4 for sustained tension; multiply by 0.8 (cracked) or 0.4 (uncracked) for seismic*

#### 4.5.4 Influence Area Calculations

**Reference Area:**
$$A_{Nao} = (2c_{Na})^2$$

Where:
$$c_{Na} = 10d_a\sqrt{\frac{77}{\tau_{uncr}}} \quad \text{or} \quad c_{Na} = 7.6d_a\sqrt{\frac{10}{\tau_{uncr}}} \text{ [SI]}$$

**Actual Influence Area ($A_{Na}$):**
- Project outward $c_{Na}$ from anchor centerline(s)
- Limited by: $A_{Na} \le n \times A_{Nao}$

#### 4.5.5 Modification Factors

**Eccentricity Factor ($\psi_{ec,Na}$):** [Group only]
$$\psi_{ec,Na} = \frac{1}{1 + \frac{e'_N}{c_{Na}}} \le 1.0$$

**Edge Effect Factor ($\psi_{ed,Na}$):**
- If $c_{a,min} \ge c_{Na}$: $\psi_{ed,Na} = 1.0$
- If $c_{a,min} < c_{Na}$: $\psi_{ed,Na} = 0.7 + 0.3\frac{c_{a,min}}{c_{Na}}$

**Splitting Factor ($\psi_{cp,Na}$):** [Uncracked concrete without reinforcement]
- If $c_{a,min} \ge c_{ac}$: $\psi_{cp,Na} = 1.0$
- If $c_{a,min} < c_{ac}$: $\psi_{cp,Na} = \frac{c_{a,min}}{c_{ac}} + \frac{c_{Na}}{c_{ac}}$
- Otherwise: $\psi_{cp,Na} = 1.0$

#### 4.5.6 Sustained Tension Check [Sec 17.5.2.2]

For adhesive anchors subjected to sustained tension:
$$\phi N_{ba} \ge N_{ua,s}$$

Where $N_{ua,s}$ = factored sustained tension component.

---

## 5. Shear Strength Design [Sec 17.7]

All applicable failure modes must be checked. Design strength = **minimum** of all modes.

### 5.1 Steel Strength in Shear ($V_{sa}$) [Sec 17.7.1]

#### 5.1.1 Cast-in Headed Studs (welded)

$$V_{sa} = A_{se,V} \times f_{uta}$$

#### 5.1.2 All Other Anchors

$$V_{sa} = 0.6 \times A_{se,V} \times f_{uta}$$

**Post-installed with sleeve through shear plane:** $V_{sa}$ determined by testing or use equation above.

**Grouted Installation:** Multiply $V_{sa}$ by **0.80**

Where:
- $f_{uta} \le \min(1.9f_{ya}, 8750 \text{ kgf/cm}^2)$

**Check:** $\phi V_{sa} \ge V_{ua}$ for **each anchor** (considering load distribution)

---

### 5.2 Concrete Breakout in Shear ($V_{cb}$ or $V_{cbg}$) [Sec 17.7.2]

#### 5.2.1 Single Anchor (Shear toward edge)

$$V_{cb} = \frac{A_{Vc}}{A_{Vco}} \times \psi_{ed,V} \times \psi_{c,V} \times \psi_{h,V} \times V_b$$

#### 5.2.2 Anchor Group (Shear toward edge)

$$V_{cbg} = \frac{A_{Vc}}{A_{Vco}} \times \psi_{ec,V} \times \psi_{ed,V} \times \psi_{c,V} \times \psi_{h,V} \times V_b$$

#### 5.2.3 Shear Parallel to Edge

$V_{cb}$ or $V_{cbg}$ = **2 times** value calculated above with $\psi_{ed,V} = 1.0$

#### 5.2.4 Corner Location

Evaluate for **both edges** and take minimum.

#### 5.2.5 Basic Concrete Breakout in Shear ($V_b$)

$$V_b = 1.86 \times \left(\frac{\ell_e}{d_a}\right)^{0.2} \times \sqrt{d_a} \times \lambda_a \times \sqrt{f'_c} \times c_{a1}^{1.5}$$

$$V_b = 0.6 \times \left(\frac{\ell_e}{d_a}\right)^{0.2} \times \sqrt{d_a} \times \lambda_a \times \sqrt{f'_c} \times c_{a1}^{1.5} \text{ [SI]}$$

**Upper Limit:**
$$V_b \le 3.8 \lambda_a \sqrt{f'_c} \times c_{a1}^{1.5} \quad \text{or} \quad 3.7 \lambda_a \sqrt{f'_c} \times c_{a1}^{1.5} \text{ [SI]}$$

**Load-Bearing Length ($\ell_e$):**
- Anchors with constant stiffness over embedment: $\ell_e = h_{ef}$
- Torque-controlled expansion with standoff sleeve: $\ell_e = 2d_a$
- In all cases: $\ell_e \le 8d_a$

**Enhanced Strength for Welded Studs:** [If continuously welded to thick attachment]

Requirements:
- Attachment thickness ≥ max($0.5d_a$, 1 cm)
- Anchor spacing $s \ge 6.5$ cm
- Corner reinforcement if $c_{a2} < 1.5h_{ef}$
- Strength based on farthest anchor row

$$V_b = 2.12 \times \left(\frac{\ell_e}{d_a}\right)^{0.2} \times \sqrt{d_a} \times \lambda_a \times \sqrt{f'_c} \times c_{a1}^{1.5}$$

(still limited by upper limit equation above)

#### 5.2.6 Projected Area Calculations

**Reference Area:**
$$A_{Vco} = 4.5c_{a1}^2$$

**Actual Projected Area ($A_{Vc}$):**
- Half-pyramid projecting from critical anchor row
- Base: $3c_{a1}$ parallel to edge × $1.5c_{a1}$ perpendicular
- Height: $1.5c_{a1}$ from surface
- Limited by: $A_{Vc} \le n \times A_{Vco}$

**Special Case - Thin/Narrow Members:** When $c_{a2}$ and $h_a < 1.5c_{a1}$:
$$c'_{a1} = \max\left(\frac{c_{a2,max}}{1.5}, \frac{h_a}{1.5}, \frac{s_{max}}{3}\right)$$
Use $c'_{a1}$ in place of $c_{a1}$ for all calculations.

**Load Distribution in Groups:**
When $s > c_{a1,1}$ (front anchor edge distance):
- **Case 1:** Half load on front anchor row ($c_{a1} = c_{a1,1}$)
- **Case 2:** All load on rear anchor row ($c_{a1} = c_{a1,2}$) - use for welded to common plate
- **Case 3:** All load on front row if $s < c_{a1,1}$ (non-welded only)

Evaluate controlling case.

#### 5.2.7 Modification Factors

**Eccentricity Factor ($\psi_{ec,V}$):** [Group only]
$$\psi_{ec,V} = \frac{1}{1 + \frac{e'_v}{1.5c_{a1}}} \le 1.0$$

**Edge Effect Factor ($\psi_{ed,V}$):**
- If $c_{a2} \ge 1.5c_{a1}$: $\psi_{ed,V} = 1.0$
- If $c_{a2} < 1.5c_{a1}$: $\psi_{ed,V} = 0.7 + 0.3\frac{c_{a2}}{1.5c_{a1}}$

Where $c_{a2}$ = smaller of the two perpendicular edges

**Cracking Factor ($\psi_{c,V}$):**

| Condition | Supplementary Reinforcement | $\psi_{c,V}$ |
|:----------|:----------------------------|:-------------|
| **Uncracked at service** | (any) | 1.4 |
| **Cracked at service** | None or corner bars < D13 | 1.0 |
| | ≥ D13 bars between anchors and edge | 1.2 |
| | ≥ D13 bars + stirrups/ties @ ≤ 10 cm spacing | 1.4 |

**Member Thickness Factor ($\psi_{h,V}$):**
- If $h_a \ge 1.5c_{a1}$: $\psi_{h,V} = 1.0$
- If $h_a < 1.5c_{a1}$: $\psi_{h,V} = \sqrt{\frac{1.5c_{a1}}{h_a}}$

---

### 5.3 Concrete Pryout Strength ($V_{cp}$ or $V_{cpg}$) [Sec 17.7.3]

#### 5.3.1 Single Anchor

$$V_{cp} = k_{cp} \times N_{cp}$$

#### 5.3.2 Anchor Group

$$V_{cpg} = k_{cp} \times N_{cpg}$$

**Pryout Factor ($k_{cp}$):**
- If $h_{ef} < 6.5$ cm: $k_{cp} = 1.0$
- If $h_{ef} \ge 6.5$ cm: $k_{cp} = 2.0$

**Pryout Resistance:**
- **Cast-in, expansion, undercut, screw:** $N_{cp}$ or $N_{cpg}$ = concrete breakout strength from Sec 17.6.2
- **Adhesive:** $N_{cp}$ or $N_{cpg}$ = **minimum** of bond strength (Sec 17.6.5) and concrete breakout (Sec 17.6.2)

---

## 6. Tension-Shear Interaction [Sec 17.8]

### 6.1 Interaction Not Required

Skip interaction check if **both** conditions are met:
$$\frac{N_{ua}}{\phi N_n} \le 0.2 \quad \text{AND} \quad \frac{V_{ua}}{\phi V_n} \le 0.2$$

### 6.2 Interaction Required

When either ratio exceeds 0.2:
$$\frac{N_{ua}}{\phi N_n} + \frac{V_{ua}}{\phi V_n} \le 1.2$$

Where:
- $N_n$ = governing **nominal** tension strength (minimum of all tension modes)
- $V_n$ = governing **nominal** shear strength (minimum of all shear modes)
- $\phi$ = appropriate strength reduction factor for each mode

---

## 7. Minimum Spacing, Edge Distance & Thickness [Sec 17.9]

To avoid splitting failure during installation or loading, anchors must meet minimum requirements **unless supplementary reinforcement is provided** to control splitting.

### 7.1 Minimum Spacing and Edge Distance [Table 17.9.2a & 17.9.2b]

| Parameter | Cast-in (No Torque) | Cast-in (With Torque) | Post-installed (Adhesive, Expansion, Undercut) | Screw Anchors |
|:----------|:-------------------|:---------------------|:-----------------------------------------------|:--------------|
| **Min Spacing ($s_{min}$)** | $4d_a$ | $6d_a$ | $6d_a$ | Greater of $0.6h_{ef}$ and $6d_a$ |
| **Min Edge Distance ($c_{a,min}$)** | Cover per Sec 20.5.1 | $6d_a$ | Greater of: (a) Cover per 20.5.1, (b) 2× max aggregate, (c) Per Table 17.9.2b or evaluation | Same as adjacent |

**Table 17.9.2b - Post-installed Minimum Edge Distance** (when product-specific data unavailable):

| Post-installed Type | $c_{a,min}$ |
|:-------------------|:-----------|
| Torque-controlled expansion | $8d_a$ |
| Displacement-controlled expansion | $10d_a$ |
| Screw anchors | $6d_a$ |
| Undercut anchors | $6d_a$ |
| Adhesive anchors | $6d_a$ |

### 7.2 Reduced Spacing/Edge Distance [Sec 17.9.3]

For non-torqued anchors that don't induce splitting during installation:

If actual spacing or edge distance < minimum required, the anchor may still be used if:
1. Calculate all strengths using an **adjusted diameter** $d'_a$ that satisfies minimum requirements
2. Verify actual anchor can resist forces based on $d'_a$

Example: If $c_a = 8$ cm but minimum requires $c_{a,min} = 6d_a$:
- Use $d'_a = 8/6 = 1.33$ cm for all strength calculations
- Actual anchor must be ≥ 1.33 cm diameter

### 7.3 Maximum Embedment for Post-installed [Sec 17.9.4]

For expansion and undercut anchors (not adhesive):
$h_{ef} \le \max\left(\frac{2h_a}{3}, h_a - 10 \text{ cm}\right)$

*Prevents back-face blowout during installation*

### 7.4 Critical Edge Distance ($c_{ac}$) [Sec 17.9.5]

**Table 17.9.5** - Used for splitting modification factors:

| Anchor Type | $c_{ac}$ |
|:-----------|:---------|
| Torque-controlled expansion | $4h_{ef}$ |
| Displacement-controlled expansion | $4h_{ef}$ |
| Screw anchors | $4h_{ef}$ |
| Undercut anchors | $2.5h_{ef}$ |
| Adhesive anchors | $2h_{ef}$ |

*Product-specific values from qualification testing may be used instead*

---

## 8. Seismic Design Requirements [Sec 17.10]

### 8.1 Applicability [Sec 17.10.1]

All anchors in seismic design structures must comply with Section 17.10 provisions **unless:**
- Exemption per Sec 17.10.5.1 (tension) or 17.10.6.1 (shear) applies
- Anchor is located in plastic hinge zone (Sec 17.10.2) - special detailing required

**Post-installed Anchors:** Must meet seismic qualification per mechanical/adhesive anchor criteria, including cyclic testing.

**Plastic Hinge Zones:** Anchors in zones expected to develop plastic hinges (typically within 2× member depth from critical section) **cannot** rely on concrete strength. Loads must transfer directly to reinforcement anchored outside plastic zone.

### 8.2 Reinforcement Requirements [Sec 17.10.4]

Anchor reinforcement in seismic structures limited to:
- CNS 560: SD280W, SD420W, SD280, SD420 deformed bars
- Must meet requirements of Section 20.2.2

### 8.3 Tension Design for Seismic [Sec 17.10.5]

#### 8.3.1 Exemption from Seismic Provisions [17.10.5.1]

Seismic provisions **not required** if:
$\frac{N_{ua,eq}}N_{ua,total} \le 0.20$

Where:
- $N_{ua,eq}$ = tension from earthquake load in the load combination
- $N_{ua,total}$ = total factored tension in same load combination

If exempt, design per Section 17.6 with standard $\phi$ factors.

#### 8.3.2 Seismic Tension Design [17.10.5.2 - 17.10.5.5]

When exemption doesn't apply, design using **ONE** of the following approaches:

**Option (a): Ductile Steel Yields First** [17.10.5.3(a)]

Requirements:
1. Steel strength controls: $1.2N_{sa} \ge N_{concrete}$ (for each tension mode)
2. For groups: $\frac{N_{ua,i}}{N_{sa,i}} \ge \frac{N_{ua,g}}{N_{concrete,g}}$ where subscript $i$ = highest stressed anchor
3. Ductile steel element with elongation length ≥ $8d_a$ outside concrete
4. Buckling prevention under cyclic load
5. If threaded, $f_{uta}/f_{ya} \ge 1.3$ (unless threads are upset/enlarged)
6. Reinforcement (if used): CNS 560 SD280W, SD420W, SD280, or SD420 only

Where:
- $N_{sa}$ = steel tension strength
- $N_{concrete}$ = minimum of $N_{cb}$, $N_{pn}$, $N_{sb}$, or $N_a$ (concrete-controlled modes)

If requirements met:
- Design for factored seismic loads from structural analysis
- Use **standard** $\phi$ factors (no 0.75 reduction)
- Concrete assumed cracked unless proven otherwise

**Option (b): Attachment Yields** [17.10.5.3(b)]

- Anchor designed for maximum force attachment can deliver through ductile yielding mechanism
- Consider material overstrength and strain hardening
- Account for $f_{ya,expected}/f_{ya,specified}$ ratio (typically 1.5 if only specified strength known, 1.25 if actual strength known)

**Option (c): Non-Yielding Attachment** [17.10.5.3(c)]

- Anchor designed for maximum seismic demand transmitted through non-yielding attachment
- All structural elements in load path remain elastic

**Option (d): Overstrength Design** [17.10.5.3(d)]

- Anchor designed using load combinations with $E_h$ amplified by overstrength factor $\Omega_o$

For Options (b), (c), and (d):

**Reduced Design Strengths** [17.10.5.4]:
$\phi N_n = \min \begin{cases}
\phi N_{sa} & \text{(use standard } \phi \text{)} \\
0.75 \phi N_{cb} \text{ or } 0.75 \phi N_{cbg} & \text{(unless anchor reinforcement per 17.5.2.1(a))} \\
0.75 \phi N_{pn} \\
0.75 \phi N_{sb} \text{ or } 0.75 \phi N_{sbg} \\
0.75 \phi N_a \text{ or } 0.75 \phi N_{ag}
\end{cases}$

- Concrete assumed **cracked** unless analysis proves uncracked at service load
- $\phi$ = standard values from Table 3.1b/c
- **0.75 reduction** applies to all concrete-controlled modes

**Exception** [17.10.5.5]: If anchor reinforcement meeting Sec 17.5.2.1(a) is provided (developed both sides of breakout plane), no 0.75 reduction needed for that mode.

### 8.4 Shear Design for Seismic [Sec 17.10.6]

#### 8.4.1 Exemption from Seismic Provisions [17.10.6.1]

Seismic provisions **not required** if:
$\frac{V_{ua,eq}}{V_{ua,total}} \le 0.20$

If exempt, design per Section 17.7 with standard provisions.

#### 8.4.2 Seismic Shear Design [17.10.6.2 - 17.10.6.4]

When exemption doesn't apply, design using **ONE** of the following:

**Option (a): Attachment Yields** [17.10.6.3(a)]
- Anchor designed for maximum shear from attachment's ductile yielding mechanism
- Consider material overstrength and strain hardening
- Example: bearing yield in steel, crushing in wood

**Option (b): Non-Yielding Attachment** [17.10.6.3(b)]
- Anchor designed for maximum seismic shear through non-yielding attachment

**Option (c): Overstrength Design** [17.10.6.3(c)]
- Anchor designed using $E$ amplified by $\Omega_o$

For all options:
- Calculate $V_n$ per Section 17.7 using standard $\phi$ factors
- **No 0.75 reduction** for shear (unlike tension)
- Steel shear strength may govern to provide ductility

**Exception** [17.10.6.4]: If anchor reinforcement meeting Sec 17.5.2.1(b) is provided, use standard provisions.

### 8.5 Tension-Shear Interaction for Seismic [Sec 17.10.7]

Combined loading check per Section 17.8, using:
- Tension strength from Sec 17.10.5.4 (with 0.75 reduction if applicable)
- Shear strength from Sec 17.7 (standard, no reduction)

---

## 9. Shear Lugs [Sec 17.11]

Shear lugs are steel plates/shapes welded to baseplates to resist high shear loads through bearing against concrete.

### 9.1 General Requirements [17.11.1]

**Configuration:**
- Rectangular plates or structural shapes welded to baseplate
- Minimum **4 anchors** required (designed per Chapter 17, excluding Sec 17.5.1.2(f), (g), (h))
- Anchors resist tension and partial shear; shear lug resists majority of shear through bearing

**Vent Holes:** Horizontal baseplates require ≥ 2.5 cm diameter vent holes along each long side of shear lug (near each face) to verify concrete/grout fill and allow air escape.

**Load Distribution:**
- Shear primarily through bearing on shear lug (Sec 17.11.2)
- Partial shear on anchors: $V_{ua,i} = V_u \frac{2d_a^2}{A_{ef,sl} + n(2d_a^2)}$ (for welded anchors)
- Check anchor tension-shear interaction per Sec 17.8 with partial shear

**Embedment Ratios** [For tensioned anchors, 17.11.1.1.8]:
- $h_{ef}/h_{sl} \ge 2.5$
- $h_{ef}/c_{sl} \ge 2.5$

Where $c_{sl}$ = distance from tension anchor centerline to shear lug centerline (shear direction)

**Moment from Load Offset:** Consider moment from bearing reaction (below anchor level) and applied shear when designing anchor tension.

### 9.2 Bearing Strength of Shear Lug ($V_{brg,sl}$) [Sec 17.11.2]

$\phi V_{brg,sl} \ge V_u$

Where $\phi = 0.65$

**Nominal Bearing Strength:**
$V_{brg,sl} = 1.7 f'_c \times A_{ef,sl} \times \psi_{brg,sl}$

#### 9.2.1 Effective Bearing Area ($A_{ef,sl}$)

Area on shear lug face perpendicular to applied shear, below concrete surface:

| Component | Contributing Area |
|:----------|:------------------|
| **Shear lug plate** | From bottom of baseplate (or concrete surface if base above surface) downward to depth $2t_{sl}$ |
| **Stiffener plates** | $2t_{sl}$ each side of stiffener face |
| **Front face** | Area of stiffener front edge below concrete surface |

Where $t_{sl}$ = thickness of shear lug plate

*See Figure R17.11.2.1.1 in commentary for visual examples*

#### 9.2.2 Bearing Modification Factor ($\psi_{brg,sl}$)

Accounts for axial load effect:

**Axial Tension:**
$\psi_{brg,sl} = 1 - \frac{P_u}{n \phi N_{sa}} \le 1.0$

**No Axial Load:**
$\psi_{brg,sl} = 1.0$

**Axial Compression:**
$\psi_{brg,sl} = 1 + \frac{4P_u}{A_{bp} f'_c} \le 2.0$

Where:
- $P_u$ = factored axial load (negative for tension, positive for compression)
- $n$ = number of tension anchors
- $A_{bp}$ = baseplate bearing area on concrete/grout

#### 9.2.3 Stiffener Requirements [17.11.2.3]

If stiffeners used, length in shear direction ≥ $0.5h_{sl}$

#### 9.2.4 Multiple Shear Lugs [17.11.2.4]

Multiple shear lugs perpendicular to shear direction may be considered effective if:
- Shear stress on concrete plane between adjacent lug bases ≤ $0.2f'_c$
- Each lug designed for its own effective area $A_{ef,sl}$

Shear plane area = (net distance between lugs) × (lug width perpendicular to shear)

### 9.3 Concrete Breakout of Shear Lug ($V_{cb,sl}$) [Sec 17.11.3]

$\phi V_{cb,sl} \ge V_u$

Where $\phi = 0.65$

#### 9.3.1 Shear Perpendicular to Edge [17.11.3.1]

Use Section 17.7.2 method with modifications:

$V_{cb,sl} = \frac{A_{Vc}}{A_{Vco}} \times \psi_{ed,V} \times \psi_{c,V} \times \psi_{h,V} \times V_b$

**Basic Strength:**
$V_b = 3.8 \lambda_a \sqrt{f'_c} \times c_{a1}^{1.5}$

(Upper limit only; use equation 17.7.2.2.1b)

**Projected Area ($A_{Vc}$):**
- Half-pyramid from shear lug bearing face (not from anchors)
- Base projections: $1.5c_{a1}$ horizontally and vertically from bearing area edges
- Exclude area of shear lug itself ($A_{ef,sl}$)
- $c_{a1}$ = distance from shear lug bearing face to free edge
- $h_{ef,sl}$ = depth from concrete surface to bottom of $A_{ef,sl}$

$A_{Vco} = 4.5c_{a1}^2$

**Modification Factors:**
- $\psi_{ed,V}$, $\psi_{c,V}$, $\psi_{h,V}$ per Sec 17.7.2 using $c_{a1}$ from shear lug
- $\psi_{ec,V} = 1.0$ (not applicable for single lug)

#### 9.3.2 Shear Parallel to Edge [17.11.3.2]

Use Section 17.7.2.1(c):
- $V_{cb,sl}$ = 2× value from equation above with $\psi_{ed,V} = 1.0$
- $c_{a1}$ = distance from shear lug centerline to edge

#### 9.3.3 Corner Locations [17.11.3.3]

Evaluate for both edges; take minimum.

#### 9.3.4 Multiple Shear Lugs [17.11.3.4]

Evaluate all potential breakout planes (similar to anchor groups per Fig R17.7.2.1b).

---

## 10. Design Procedure Flowchart

```
START
  │
  ├─→ [1] Classify Anchor Type
  │    └─→ Cast-in / Post-installed / Adhesive
  │
  ├─→ [2] Verify Geometric Limits
  │    ├─→ f'c limits (Sec 17.3.1)
  │    ├─→ da ≤ 10 cm (Sec 17.3.2)
  │    ├─→ hef limits for screw/adhesive (Sec 17.3.3, 17.3.4)
  │    └─→ Spacing/edge distance (Sec 17.9)
  │
  ├─→ [3] Determine Conditions
  │    ├─→ Cracked/Uncracked at service
  │    ├─→ Supplementary reinforcement?
  │    ├─→ Seismic design required?
  │    └─→ Sustained tension (adhesive)?
  │
  ├─→ [4] Calculate TENSION Strengths
  │    ├─→ Steel: Nsa (Sec 17.6.1)
  │    ├─→ Concrete breakout: Ncb or Ncbg (Sec 17.6.2)
  │    ├─→ Pullout: Npn (Sec 17.6.3) [if applicable]
  │    ├─→ Side-face blowout: Nsb (Sec 17.6.4) [if applicable]
  │    └─→ Bond: Na or Nag (Sec 17.6.5) [adhesive only]
  │
  ├─→ [5] Calculate SHEAR Strengths
  │    ├─→ Steel: Vsa (Sec 17.7.1)
  │    ├─→ Concrete breakout: Vcb or Vcbg (Sec 17.7.2)
  │    └─→ Pryout: Vcp or Vcpg (Sec 17.7.3)
  │
  ├─→ [6] Apply Strength Reduction Factors (φ)
  │    └─→ Tables 17.5.3a/b/c
  │
  ├─→ [7] Apply Seismic Reductions (if applicable)
  │    ├─→ Tension: 0.75 factor on concrete modes (Sec 17.10.5.4)
  │    └─→ Shear: standard φ (no 0.75)
  │
  ├─→ [8] Check Basic Strength
  │    ├─→ φNn ≥ Nua
  │    └─→ φVn ≥ Vua
  │
  ├─→ [9] Check Interaction (if both > 20%)
  │    └─→ Nua/(φNn) + Vua/(φVn) ≤ 1.2
  │
  ├─→ [10] Seismic Ductility Check (if applicable)
  │    └─→ Option (a): 1.2Nsa ≥ Nconcrete
  │        OR Options (b), (c), (d)
  │
  └─→ [PASS / FAIL]
       └─→ Report controlling mode
```

---

## 11. Key Equations Summary

### Tension Modes

| Mode | Equation | Section |
|:-----|:---------|:--------|
| **Steel** | $N_{sa} = A_{se,N} f_{uta}$ | 17.6.1 |
| **Breakout** | $N_{cb} = \frac{A_{Nc}}{A_{Nco}} \psi_{ed,N} \psi_{c,N} \psi_{cp,N} N_b$ | 17.6.2 |
| | $N_b = k_c \lambda_a \sqrt{f'_c} h_{ef}^{1.5}$ | 17.6.2.2 |
| | $A_{Nco} = 9h_{ef}^2$ | 17.6.2.1.4 |
| **Pullout** | $N_{pn} = \psi_{c,P} N_p$ | 17.6.3 |
| | Headed: $N_p = 8A_{brg}f'_c$ | 17.6.3.2.2 |
| | Hooked: $N_p = 0.9f'_c e_h d_a$ | 17.6.3.2.2 |
| **Blowout** | $N_{sb} = 42.4 c_{a1}\sqrt{A_{brg}} \lambda_a \sqrt{f'_c}$ | 17.6.4 |
| **Bond** | $N_a = \frac{A_{Na}}{A_{Nao}} \psi_{ed,Na} \psi_{cp,Na} N_{ba}$ | 17.6.5 |
| | $N_{ba} = \lambda_a \tau_{cr} \pi d_a h_{ef}$ | 17.6.5.2 |
| | $A_{Nao} = (2c_{Na})^2$ where $c_{Na} = 10d_a\sqrt{77/\tau_{uncr}}$ | 17.6.5.1.2 |

### Shear Modes

| Mode | Equation | Section |
|:-----|:---------|:--------|
| **Steel** | $V_{sa} = A_{se,V} f_{uta}$ (welded studs) | 17.7.1 |
| | $V_{sa} = 0.6 A_{se,V} f_{uta}$ (others) | 17.7.1 |
| **Breakout** | $V_{cb} = \frac{A_{Vc}}{A_{Vco}} \psi_{ed,V} \psi_{c,V} \psi_{h,V} V_b$ | 17.7.2 |
| | $V_b = 1.86(\ell_e/d_a)^{0.2}\sqrt{d_a} \lambda_a \sqrt{f'_c} c_{a1}^{1.5}$ | 17.7.2.2 |
| | Upper: $V_b \le 3.8\lambda_a\sqrt{f'_c} c_{a1}^{1.5}$ | 17.7.2.2 |
| | $A_{Vco} = 4.5c_{a1}^2$ | 17.7.2.1.2 |
| **Pryout** | $V_{cp} = k_{cp} N_{cp}$ | 17.7.3 |
| | $k_{cp} = 1.0$ (if $h_{ef} < 6.5$ cm) or $2.0$ | 17.7.3.1 |

### Interaction

| Check | Equation | Section |
|:------|:---------|:--------|
| **Exemption** | $N_{ua}/(\phi N_n) \le 0.2$ **AND** $V_{ua}/(\phi V_n) \le 0.2$ | 17.8.2 |
| **Required** | $\frac{N_{ua}}{\phi N_n} + \frac{V_{ua}}{\phi V_n} \le 1.2$ | 17.8.3 |

### Shear Lugs

| Check | Equation | Section |
|:------|:---------|:--------|
| **Bearing** | $V_{brg,sl} = 1.7f'_c A_{ef,sl} \psi_{brg,sl}$ | 17.11.2 |
| **Breakout** | $V_{cb,sl} = \frac{A_{Vc}}{A_{Vco}} \psi_{ed,V} \psi_{c,V} \psi_{h,V} V_b$ | 17.11.3 |
| | $V_b = 3.8\lambda_a\sqrt{f'_c} c_{a1}^{1.5}$ (upper limit) | 17.11.3 |

---

## 12. Modification Factors ($\psi$) Quick Reference

### Tension

| Factor | Condition | Formula | Section |
|:-------|:----------|:--------|:--------|
| $\psi_{ec,N}$ | Eccentric group load | $1/(1 + e'_N/1.5h_{ef}) \le 1.0$ | 17.6.2.3 |
| $\psi_{ed,N}$ | Edge distance effect | $c_{a,min} < 1.5h_{ef}$: $0.7 + 0.3(c_{a,min}/1.5h_{ef})$ | 17.6.2.4 |
| $\psi_{c,N}$ | Cracking condition | Uncracked: 1.25 (cast-in), 1.4 (post); Cracked: 1.0 | 17.6.2.5 |
| $\psi_{cp,N}$ | Splitting (post, uncracked) | $c_{a,min} < c_{ac}$: $(c_{a,min}/c_{ac}) + 0.3(h_{ef}/c_{ac})$ | 17.6.2.6 |
| $\psi_{c,P}$ | Pullout cracking | Uncracked: 1.4; Cracked: 1.0 | 17.6.3.3 |

### Bond (Adhesive)

| Factor | Condition | Formula | Section |
|:-------|:----------|:--------|:--------|
| $\psi_{ec,Na}$ | Eccentric group load | $1/(1 + e'_N/c_{Na}) \le 1.0$ | 17.6.5.3 |
| $\psi_{ed,Na}$ | Edge distance effect | $c_{a,min} < c_{Na}$: $0.7 + 0.3(c_{a,min}/c_{Na})$ | 17.6.5.4 |
| $\psi_{cp,Na}$ | Splitting (uncracked) | $c_{a,min} < c_{ac}$: $(c_{a,min}/c_{ac}) + (c_{Na}/c_{ac})$ | 17.6.5.5 |

### Shear

| Factor | Condition | Formula | Section |
|:-------|:----------|:--------|:--------|
| $\psi_{ec,V}$ | Eccentric group load | $1/(1 + e'_v/1.5c_{a1}) \le 1.0$ | 17.7.2.3 |
| $\psi_{ed,V}$ | Edge distance effect | $c_{a2} < 1.5c_{a1}$: $0.7 + 0.3(c_{a2}/1.5c_{a1})$ | 17.7.2.4 |
| $\psi_{c,V}$ | Cracking + reinforcement | Uncracked: 1.4; Cracked no reinf: 1.0; +D13 bars: 1.2; +stirrups: 1.4 | 17.7.2.5 |
| $\psi_{h,V}$ | Member thickness | $h_a < 1.5c_{a1}$: $\sqrt{1.5c_{a1}/h_a}$ | 17.7.2.6 |

---

## 13. Output Specification

For each analysis run, the system shall output:

### 13.1 Input Echo
- All input parameters with units
- Anchor classification and category
- Applied loads and load combinations
- Condition flags (cracked, seismic, sustained, etc.)

### 13.2 Intermediate Calculations
- All modification factors ($\psi$ values) with justification
- Projected areas ($A_{Nc}$, $A_{Vc}$, etc.)
- Basic strengths ($N_b$, $V_b$, $N_p$, etc.)
- Individual mode nominal strengths before $\phi$

### 13.3 Design Strength Results

| Output Variable | Description | Formula | Status |
|:----------------|:------------|:--------|:-------|
| **φN_sa** | Steel tension strength | $\phi A_{se,N} f_{uta}$ | PASS/FAIL vs $N_{ua}$ |
| **φN_cb** | Concrete breakout tension | $\phi \times$ (Sec 17.6.2 result) | PASS/FAIL vs $N_{ua}$ |
| **φN_pn** | Pullout strength | $\phi \psi_{c,P} N_p$ | PASS/FAIL vs $N_{ua}$ |
| **φN_sb** | Side-face blowout | $\phi \times$ (Sec 17.6.4 result) | PASS/FAIL vs $N_{ua}$ |
| **φN_a** | Bond strength (adhesive) | $\phi \times$ (Sec 17.6.5 result) | PASS/FAIL vs $N_{ua}$ |
| **φN_n** | **Governing tension** | **min(all tension modes)** | **PASS/FAIL** |
| **φV_sa** | Steel shear strength | $\phi \times 0.6 A_{se,V} f_{uta}$ | PASS/FAIL vs $V_{ua}$ |
| **φV_cb** | Concrete breakout shear | $\phi \times$ (Sec 17.7.2 result) | PASS/FAIL vs $V_{ua}$ |
| **φV_cp** | Pryout strength | $\phi k_{cp} N_{cp}$ | PASS/FAIL vs $V_{ua}$ |
| **φV_n** | **Governing shear** | **min(all shear modes)** | **PASS/FAIL** |

### 13.4 Utilization Ratios

| Ratio | Formula | Limit | Status |
|:------|:--------|:------|:-------|
| **Tension Utilization** | $\frac{N_{ua}}{\phi N_n}$ | ≤ 1.0 | Value + PASS/FAIL |
| **Shear Utilization** | $\frac{V_{ua}}{\phi V_n}$ | ≤ 1.0 | Value + PASS/FAIL |
| **Interaction** | $\frac{N_{ua}}{\phi N_n} + \frac{V_{ua}}{\phi V_n}$ | ≤ 1.2 | Value + PASS/FAIL + (EXEMPT if ratios ≤ 0.2) |

### 13.5 Seismic-Specific Outputs (if applicable)

| Check | Result | Status |
|:------|:-------|:-------|
| **Seismic Exemption (Tension)** | $N_{ua,eq}/N_{ua,total}$ | EXEMPT / NOT EXEMPT |
| **Seismic Exemption (Shear)** | $V_{ua,eq}/V_{ua,total}$ | EXEMPT / NOT EXEMPT |
| **Ductility Check** | $1.2N_{sa}$ vs $N_{concrete}$ | PASS / FAIL / N/A |
| **Embedment for Ductility** | Yielding length / $8d_a$ | ADEQUATE / INADEQUATE / N/A |
| **0.75 Reduction Applied** | Modes affected | List of modes |

### 13.6 Controlling Failure Mode

Output shall explicitly identify the **governing limit state** with format:

```
CONTROLLING MODE: [Mode Type] - [Specific Condition]
Design Strength: φ[Mode] = [value] kgf
Demand: [Load Type] = [value] kgf
Utilization: [value]%

Examples:
- "CONTROLLING MODE: Concrete Breakout in Tension - Cracked concrete without edge reinforcement"
- "CONTROLLING MODE: Steel Strength in Shear - Anchor threads"
- "CONTROLLING MODE: Tension-Shear Interaction - Combined loading"
- "CONTROLLING MODE: Pryout - Shallow embedment (hef < 6.5 cm)"
- "CONTROLLING MODE: Bond Strength - Adhesive anchor sustained loading"
```

### 13.7 Warnings and Notes

System shall flag:

**Critical Warnings:**
- ❌ Embedment exceeds limits (Sec 17.3.2, 17.3.3, 17.3.4, 17.9.4)
- ❌ Spacing/edge distance below minimums (Sec 17.9.2)
- ❌ Concrete strength exceeds limits (Sec 17.3.1)
- ❌ Post-installed anchor lacks qualification data
- ❌ Seismic ductility requirements not met
- ❌ Anchor in plastic hinge zone (Sec 17.10.2)

**Advisory Notes:**
- ⚠️ Anchor group effects active ($s < 3h_{ef}$ for tension, $s < 3c_{a1}$ for shear)
- ⚠️ Thin member adjustments applied (Sec 17.6.2.1.2 or 17.7.2.1.1)
- ⚠️ Edge effects reducing strength ($c_a < 1.5h_{ef}$ or $c_{a1}$)
- ⚠️ Sustained tension check required for adhesive (Sec 17.5.2.2)
- ⚠️ Cracked concrete assumed - verify with structural analysis
- ⚠️ Supplementary reinforcement recommended for improved ductility
- ⚠️ Installation-sensitive anchor (Category 2 or 3) - special inspection required

**Design Recommendations:**
- ℹ️ Increase embedment depth to improve strength
- ℹ️ Increase edge distance to reduce edge effects
- ℹ️ Add supplementary reinforcement to increase φ factors
- ℹ️ Consider larger diameter anchor
- ℹ️ Reduce anchor spacing to distribute loads
- ℹ️ Use cast-in anchors for better seismic performance

---

## 14. Special Considerations and Design Guidance

### 14.1 Lightweight Concrete [Sec 17.2.4]

**Table 17.2.4.1 - Modification Factor λₐ:**

| Failure Condition | Cast-in & Undercut | Expansion, Screw, Adhesive |
|:------------------|:-------------------|:---------------------------|
| Concrete breakout, blowout, pryout | 1.0 | 0.8 |
| Bond failure (adhesive anchors) | — | 0.6 |

*Values determined per Section 19.2.4*
*Product-specific values from qualification testing may be used*

### 14.2 Adhesive Anchor Special Requirements

**Installation Sensitivity [Sec 17.2.3, 17.6.5.2.5]:**
- Overhead or upward-sloped installation requires special qualification
- Drilling method affects bond: rotary hammer or rotary preferred
- Hole cleanliness critical: dry, saturated, or water-filled conditions
- Temperature during installation: minimum 10°C
- Concrete age at installation: minimum 21 days
- Concrete strength at installation: minimum 175 kgf/cm² [17 MPa]

**Sustained Tension [Sec 17.5.2.2]:**
$\phi N_{ba} \ge N_{ua,s}$

Where $N_{ua,s}$ = sustained tension component (e.g., dead load)

**Temperature Effects:**
- Table 17.6.5.2.5 provides minimum τ values for specific conditions
- Indoor (dry, ≤43°C): τ_cr = 21 kgf/cm², τ_uncr = 70 kgf/cm²
- Outdoor (saturated, ≤79°C): τ_cr = 14 kgf/cm², τ_uncr = 45 kgf/cm²
- For sustained tension: multiply by 0.4
- For seismic: multiply by 0.8 (cracked) or 0.4 (uncracked)

### 14.3 Edge Distance Optimization

**Tension-Controlled Design:**
- Increase ca to ca ≥ 1.5hef to eliminate edge effects (ψed,N = 1.0)
- For adhesive: increase to ca ≥ cNa for same benefit
- Consider deeper embedment if edge distance limited

**Shear-Controlled Design:**
- Primary effect: increase ca1 (distance to edge in shear direction)
- Strength proportional to ca1^1.5
- Secondary effect: ensure ca2 ≥ 1.5ca1 for ψed,V = 1.0

### 14.4 Anchor Group Design Strategy

**Load Distribution:**
- Elastic analysis unless steel-controlled and ductile
- Plastic analysis allowed when steel yields govern
- Consider eccentricity effects (ψec factors)

**Spacing Optimization:**
- Tension: s ≥ 3hef avoids group effects (full ANc for each anchor)
- Shear: s ≥ 3ca1 avoids interaction
- Closer spacing: use group equations with reduced ANC/AVc

**Multiple Load Paths:**
- For critical connections, design so anchor reinforcement can replace concrete modes
- Provides redundancy if concrete cracking exceeds assumptions

### 14.5 Construction Tolerances

**Cast-in Anchors:**
- Position tolerance: typically ±6 mm for anchor location
- Embedment tolerance: ±25 mm depth
- Verify actual position before finalizing attachment design

**Post-installed Anchors:**
- Hole diameter: per manufacturer (typically +2-3 mm for adhesive)
- Hole depth: hef + 1da minimum clearance
- Cleaning requirements: critical for adhesive (brush, blow, brush method)

### 14.6 Inspection and Testing

**Required Documentation:**
- Manufacturer's product evaluation report (ESR or equivalent)
- Installation instructions
- Proof of qualification testing per mechanical/adhesive criteria
- Material certifications for steel

**Special Inspection [Sec 26.13]:**
- Required for post-installed anchors resisting seismic loads
- Verify hole diameter, depth, cleanliness
- Installation torque/displacement verification
- Pull-test requirements per project specifications

**Periodic Testing:**
- Proof testing: typically 125% of service load
- Frequency per project specifications
- Document all test locations and results

### 14.7 Cracking Assessment

**Assume Cracked (ψc = 1.0) When:**
- Flexural tension zone under service loads
- Areas with controlled cracking per Section 24.3.2
- Seismic design (unless proven otherwise)
- Shrinkage-restraint zones per Section 24.4.2

**May Use Uncracked (ψc > 1.0) When:**
- Compression zone under all load combinations
- Minimum reinforcement per Section 24.4 provided
- Analysis shows no tension at service loads
- Special pre-compression maintained

### 14.8 Failure Mode Preference

**Optimal Design Hierarchy:**
1. **Ductile steel yielding** (tension: fut/fya ≥ 1.3, elongation ≥14%; shear: attachment yielding)
2. **Concrete breakout with reinforcement** (higher φ, better ductility)
3. **Concrete breakout without reinforcement** (moderate ductility)
4. **Pullout** (limited ductility)
5. **Bond failure** (brittle for adhesive)
6. **Side-face blowout** (avoid through adequate edge distance)

**Avoid:**
- Brittle steel failure (low elongation anchors)
- Splitting failure (maintain minimum spacing/edge distance)
- Seismic design controlled by concrete modes without ductility provisions

### 14.9 Common Design Errors

❌ **Errors to Avoid:**

1. **Forgetting group effects** - Always check if s < 3hef (tension) or s < 3ca1 (shear)
2. **Mixing single and group equations** - Use Ncb for single, Ncbg for group
3. **Wrong phi factor** - Different φ for steel, concrete, pullout modes
4. **Ignoring thin member effects** - Apply Sec 17.6.2.1.2 or 17.7.2.1.1 when multiple edges close
5. **Seismic 0.75 on everything** - Only applies to concrete tension modes, not steel or shear
6. **Using fut > 1.9fya or 8750 kgf/cm²** - Always limit per 17.6.1.2
7. **Forgetting interaction check** - Required when both ratios > 0.2
8. **Wrong cracking assumption** - Verify service load condition
9. **Ignoring sustained tension for adhesive** - Must check Sec 17.5.2.2
10. **Using post-installed without qualification data** - Must have ESR or equivalent

### 14.10 Advanced Topics

**Reinforced Concrete Connections:**
- Use anchor reinforcement (Sec 17.5.2.1) to bypass concrete modes
- Reinforcement must develop per Chapter 25 on both sides of breakout plane
- Provides significantly higher strength and ductility
- Particularly beneficial for seismic applications

**Corrosion Protection:**
- Chapter 17 addresses structural capacity only
- Durability provisions per Section 20.5 (cover requirements)
- Aggressive environments: consider stainless steel, galvanized, or epoxy-coated
- Dissimilar metal contact: insulation may be required

**Temperature Effects:**
- Adhesive anchors particularly sensitive (see Table 17.6.5.2.5)
- High temperature reduces bond strength
- Cold temperature affects adhesive curing
- Cyclic temperature can cause grip loss in mechanical anchors

**Fatigue Loading:**
- Chapter 17 excludes high-cycle fatigue (Sec 17.1.4)
- For fatigue-loaded connections, consult:
  - ACI 355.2/355.4 qualification reports with fatigue data
  - Manufacturer's recommendations
  - Research literature (fib Bulletin 58)

---

## 15. Code Compliance Matrix

| Requirement | Code Section | Implementation Status |
|:------------|:-------------|:---------------------|
| **Input Validation** |
| f'c limits | 17.3.1 | ✓ Complete |
| d_a ≤ 10 cm | 17.3.2 | ✓ Complete |
| h_ef limits (adhesive) | 17.3.3 | ✓ Complete |
| h_ef limits (screw) | 17.3.4 | ✓ Complete |
| Spacing/edge distance | 17.9 | ✓ Complete |
| **Tension Strength** |
| Steel strength (N_sa) | 17.6.1 | ✓ Complete |
| Concrete breakout (N_cb) | 17.6.2 | ✓ Complete |
| Pullout (N_pn) | 17.6.3 | ✓ Complete |
| Side-face blowout (N_sb) | 17.6.4 | ✓ Complete |
| Bond strength (N_a) | 17.6.5 | ✓ Complete |
| **Shear Strength** |
| Steel strength (V_sa) | 17.7.1 | ✓ Complete |
| Concrete breakout (V_cb) | 17.7.2 | ✓ Complete |
| Pryout (V_cp) | 17.7.3 | ✓ Complete |
| **Interaction** |
| Tension-shear | 17.8 | ✓ Complete |
| **Seismic** |
| Tension requirements | 17.10.5 | ✓ Complete |
| Shear requirements | 17.10.6 | ✓ Complete |
| Ductility checks | 17.10.5.3 | ✓ Complete |
| **Shear Lugs** |
| Bearing strength | 17.11.2 | ✓ Complete |
| Breakout strength | 17.11.3 | ✓ Complete |

---

## 16. References

### Primary Code Documents
- **Chapter 17** - Anchoring to Concrete (full code text provided in Chinese)
- ACI 318-19: Building Code Requirements for Structural Concrete
- ACI 355.2: Qualification of Post-Installed Mechanical Anchors in Concrete
- ACI 355.4: Qualification of Post-Installed Adhesive Anchors in Concrete

---
