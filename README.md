# Anchor Analysis - ACI/Structural Compliance Tool
## Overview

**Anchor Analysis** is a web-based structural engineering application designed to analyze and verify concrete anchor connections according to ACI 318 standards designed by FengYu Group. The tool performs comprehensive calculations for both single anchors and anchor groups under tension and shear loading conditions.

## Features

### Core Functionality
- **Single Anchor Analysis**: Concrete breakout strength calculations for tension and shear
- **Group Anchor Analysis**: Group effect calculations with eccentricity factors
- **Real-time Calculations**: Instant structural verification with pass/fail indicators
- **Multi-language Support**: English and Traditional Chinese (繁體中文)
- **Cloud Synchronization**: Firebase-powered data storage and retrieval
- **PDF Report Generation**: Professional calculation reports with detailed breakdowns

### Database Management
- Save calculation history to cloud
- Load and restore previous calculations
- View all stored records with status indicators
- Bulk delete functionality

## Technical Implementation

### Design Parameters

#### 1. Material & Geometry
- **f'<sub>c</sub>**: Concrete compressive strength (kgf/cm²)
- **h<sub>a</sub>**: Wall thickness (cm)
- **d<sub>a</sub>**: Anchor diameter (cm)
- **h<sub>ef</sub>**: Embedment depth (cm)

#### 2. Spacing & Edge Distances
- **S<sub>1</sub>**: Vertical spacing between anchors (cm)
- **S<sub>2</sub>**: Horizontal spacing between anchors (cm)
- **C<sub>a1</sub>**: Top edge distance (cm)
- **C<sub>a1,1</sub>**: Bottom edge distance (cm)
- **C<sub>a2</sub>**: Side edge distance (cm)

#### 3. Factors & Coefficients
- **k<sub>c</sub>**: Anchor type coefficient
  - Cast-in anchors: 10
  - Post-installed anchors: 7
  
- **λ<sub>a</sub>**: Concrete failure factor
  - Cast-in/Undercut: 1.0
  - Expansion/Bonded: 0.8
  - Bonded failure: 0.6
  
- **l<sub>e</sub>**: Load bearing length
  - Default (8d<sub>a</sub>)
  - Full sleeve/headed (h<sub>ef</sub>)
  - Torque-controlled (2d<sub>a</sub>)
  
- **ψ<sub>c,V</sub>**: Cracking and reinforcement factor
  - No reinforcement / < D13: 1.0
  - Reinforcement ≥ D13: 1.2
  - Reinforcement ≥ D13 + ties: 1.4
  
- **e'<sub>N</sub>, e'<sub>V</sub>**: Eccentricity distances for tension and shear (cm)

#### 4. Applied Loads
- **T**: Tension load (kgf)
- **V**: Shear load (kgf)

## Calculation Methods

### 1. Concrete Tension Breakout (Single Anchor)

#### Basic Strength:

$$N_b = k_c \cdot \lambda_a \cdot \sqrt{f'_c} \cdot h_{ef}^{1.5}$$

#### Projected Area:

$$A_{Nc} = 2 \times (1.5 \times h_{ef}) \times (C_{a1} + S_1 + 1.5 \times h_{ef})$$

#### Reference Area:

$$A_{Nco} = 9 \times h_{ef}^2$$

#### Edge Distance Factor:

$$\psi_{ed,N} = 0.7 + 0.3 \times \frac{c_{a,min}}{1.5 \times h_{ef}} \leq 1.0$$

where:

$$c_{a,min} = \min(C_{a1}, C_{a1,1}, C_{a2})$$

#### Design Capacity:

$$\phi N_{cb} = 0.75 \times \frac{A_{Nc}}{A_{Nco}} \times \psi_{ed,N} \times \psi_{c,N} \times \psi_{cp,N} \times N_b$$

where:
- ϕ = 0.75 (strength reduction factor)
- ψ<sub>c,N</sub> = 1.0 (cracking factor for tension)
- ψ<sub>cp,N</sub> = 1.0 (splitting factor)

---

### 2. Concrete Shear Breakout (Single Anchor)

#### Basic Shear Strength:

$$V_{b1} = 1.86 \times \left(\frac{l_e}{d_a}\right)^{0.2} \times \sqrt{d_a} \times \lambda_a \times \sqrt{f'_c} \times C_{a1,1}^{1.5}$$

$$V_{b2} = 3.8 \times \lambda_a \times \sqrt{f'_c} \times C_{a1,1}^{1.5}$$

$$V_b = \min(V_{b1}, V_{b2})$$

#### Projected Area:

$$A_{Vc} = 2 \times (1.5 \times C_{a1,1}) \times h_a$$

#### Reference Area:

$$A_{Vco} = 4.5 \times C_{a1,1}^2$$

#### Height Factor:

$$\psi_{h,V} = \sqrt{\frac{1.5 \times C_{a1,1}}{h_a}} \geq 1.0$$

#### Edge Distance Factor:

$$\psi_{ed,V} = 0.7 + 0.3 \times \frac{C_{a2}}{1.5 \times C_{a1,1}} \leq 1.0$$

#### Design Capacity:

$$\phi V_{cb} = 0.75 \times \frac{A_{Vc}}{A_{Vco}} \times \psi_{ed,V} \times \psi_{c,V} \times \psi_{h,V} \times V_b$$

---

### 3. Concrete Tension Breakout (Group)

#### Projected Area (Group):

$$A_{Nc(group)} = (C_{a2} + S_2 + 1.5 \times h_{ef}) \times (C_{a1} + S_1 + 1.5 \times h_{ef})$$

#### Eccentricity Factor:

$$\psi_{ec,N} = \frac{1}{1 + \frac{2 \times e'_N}{3 \times h_{ef}}}$$

Note: If e'<sub>N</sub> = 0, then ψ<sub>ec,N</sub> = 1.0

#### Design Capacity:

$$\phi N_{cbg} = 0.75 \times \frac{A_{Nc(group)}}{A_{Nco}} \times \psi_{ec,N} \times \psi_{ed,N} \times \psi_{c,N} \times \psi_{cp,N} \times N_b$$

#### Load Check:

$$2T \leq \phi N_{cbg}$$

---

### 4. Concrete Shear Breakout (Group)

#### Projected Area (Group):

$$A_{Vc(group)} = [(2 \times 1.5 \times C_{a1,1}) + S_2] \times h_a$$

#### Eccentricity Factor:

$$\psi_{ec,V} = \frac{1}{1 + \frac{2 \times e'_V}{3 \times C_{a1,1}}}$$

Note: If e'<sub>V</sub> = 0, then ψ<sub>ec,V</sub> = 1.0

#### Design Capacity:

$$\phi V_{cbg} = 0.75 \times \frac{A_{Vc(group)}}{A_{Vco}} \times \psi_{ec,V} \times \psi_{ed,V} \times \psi_{c,V} \times \psi_{h,V} \times V_b$$

#### Load Check:

$$V \leq \phi V_{cbg}$$

---

## Result Interpretation

### Pass/Fail Criteria
- **PASS**: Demand/Capacity ratio < 1.0
- **FAIL**: Demand/Capacity ratio ≥ 1.0

### Demand/Capacity Ratios

For Single Anchor Tension:

$$\text{Ratio}_1 = \frac{T}{\phi N_{cb}}$$

For Single Anchor Shear:

$$\text{Ratio}_2 = \frac{V}{\phi V_{cb}}$$

For Group Anchor Tension:

$$\text{Ratio}_3 = \frac{2T}{\phi N_{cbg}}$$

For Group Anchor Shear:

$$\text{Ratio}_4 = \frac{V}{\phi V_{cbg}}$$

### Output for Each Check
1. **Intermediate Values**: N<sub>b</sub>, V<sub>b</sub>, A<sub>Nc</sub>, A<sub>Vc</sub>, modification factors
2. **Design Capacity**: ϕN<sub>cb</sub>, ϕV<sub>cb</sub>, ϕN<sub>cbg</sub>, ϕV<sub>cbg</sub>
3. **Applied Load**: T, V, 2T
4. **Demand/Capacity Ratio**: Load / Capacity

---

## Technology Stack

- **Frontend**: HTML5, Tailwind CSS, JavaScript
- **Backend**: Firebase Authentication & Firestore
- **Libraries**:
  - jsPDF & jsPDF-AutoTable (PDF generation)
  - Firebase SDK 9.22.0
  - Font Awesome 6.4.0

## Units Convention

| Parameter | Unit |
|-----------|------|
| Force | kgf (kilogram-force) |
| Length | cm (centimeters) |
| Area | cm² |
| Stress | kgf/cm² |

---

## Usage Guide

### Step-by-Step Process

1. **Input Parameters**: Enter all design parameters in the input fields
   - Material properties (f'<sub>c</sub>, h<sub>a</sub>)
   - Anchor geometry (d<sub>a</sub>, h<sub>ef</sub>)
   - Spacing and edge distances (S<sub>1</sub>, S<sub>2</sub>, C<sub>a1</sub>, C<sub>a1,1</sub>, C<sub>a2</sub>)
   
2. **Select Factors**: Choose appropriate values from dropdown menus
   - Anchor type coefficient (k<sub>c</sub>)
   - Failure factor (λ<sub>a</sub>)
   - Load bearing length mode (l<sub>e</sub>)
   - Cracking/reinforcement factor (ψ<sub>c,V</sub>)
   - Enter eccentricity values if applicable
   
3. **Enter Loads**: Input design loads
   - Tension load (T)
   - Shear load (V)
   
4. **Run Analysis**: Click "Run Structural Analysis" button

5. **Review Results**: Check pass/fail status for all four calculation modes
   - Single anchor tension breakout
   - Single anchor shear breakout
   - Group anchor tension breakout
   - Group anchor shear breakout

6. **Save to Cloud** (Optional): Store calculation for future reference
   - Requires user login (email/password or guest)
   
7. **Download Report**: Generate comprehensive PDF report with all calculations

---

## Firebase Configuration

The application uses Firebase for:
- User authentication (email/password and anonymous)
- Cloud storage of calculation history
- Real-time data synchronization

### Collection Structure:
```
artifacts/
  └── anchor_app/
      └── users/
          └── {userId}/
              └── history/
                  └── {documentId}
                      ├── inputs (object)
                      ├── summary (object)
                      └── timestamp
```

---

## PDF Report Contents

The generated PDF report includes:

1. **Header Section**
   - Application title and subtitle
   - Report generation date
   
2. **Design Parameters Table**
   - All material and geometry inputs
   - Spacing and edge distance values
   - Applied loads
   
3. **Factors & Coefficients Table**
   - Selected anchor type
   - Failure mode factor
   - Load bearing length mode
   - Reinforcement factor
   - Eccentricity values

4. **Calculation Results** (Four sections)
   - **Section 1**: Single Anchor Tension Breakout
     - N<sub>b</sub>, A<sub>Nc</sub>, A<sub>Nco</sub>, ψ<sub>ed,N</sub>
     - ϕN<sub>cb</sub>, Load check, D/C ratio
   
   - **Section 2**: Single Anchor Shear Breakout
     - V<sub>b</sub>, A<sub>Vc</sub>, A<sub>Vco</sub>, ψ<sub>ed,V</sub>, ψ<sub>h,V</sub>
     - ϕV<sub>cb</sub>, Load check, D/C ratio
   
   - **Section 3**: Group Anchor Tension Breakout
     - A<sub>Nc(group)</sub>, ψ<sub>ec,N</sub>
     - ϕN<sub>cbg</sub>, Load check, D/C ratio
   
   - **Section 4**: Group Anchor Shear Breakout
     - A<sub>Vc(group)</sub>, ψ<sub>ec,V</sub>
     - ϕV<sub>cbg</sub>, Load check, D/C ratio

5. **Pass/Fail Status** for each calculation mode

---

## Design Philosophy

- **User-Friendly**: Clean interface with clear visual hierarchy and intuitive navigation
- **Professional**: Engineering-grade calculations strictly following ACI 318 standards
- **Responsive**: Optimized for desktop, tablet, and mobile devices
- **Bilingual**: Complete support for English and Traditional Chinese (繁體中文)
- **Comprehensive**: Covers both single and group anchor failure modes
- **Traceable**: Complete calculation documentation via detailed PDF reports
- **Cloud-Enabled**: Save and access calculations from any device

---

## Safety Notes and Limitations

⚠️ **Important Considerations:**

1. **Strength Reduction Factor**: ϕ = 0.75 is applied to all concrete breakout capacities
2. **Automatic Calculations**: Edge distance effects (ψ<sub>ed,N</sub>, ψ<sub>ed,V</sub>) are calculated automatically
3. **Conservative Approach**: All calculations follow conservative assumptions per ACI 318
4. **Professional Review**: Results should always be verified by licensed professional engineers
5. **Code Compliance**: User is responsible for ensuring compliance with local building codes

---

## References

- **ACI 318-19**: Building Code Requirements for Structural Concrete

---

## Version Information

- **Browser Support**: Chrome, Firefox, Safari, Edge (latest versions)
- **Mobile Support**: iOS Safari, Chrome Mobile

---

## Contact & Support

For technical support, feature requests, or bug reports, please use the feedback mechanism within the application or contact gacchuguts@gmail.com.

---

**Disclaimer**: This software is provided as a calculation aid for professional engineers. The user assumes all responsibility for the accuracy and appropriateness of input data and the interpretation of results. Always verify calculations independently and ensure compliance with applicable building codes and standards.
