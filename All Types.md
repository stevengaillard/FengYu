# Concrete Anchor Analysis (Chapter 17 Compliance)

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
| **Min Edge Distance ($c_{a,min}$)** | Cover per Sec 20.5.1 | $6d_a