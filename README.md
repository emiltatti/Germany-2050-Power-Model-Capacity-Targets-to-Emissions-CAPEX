# Germany’s Power Sector Decarbonization Pathway (2025–2050)

This project builds a transparent, deterministic model of Germany’s electricity sector transition based on official capacity targets and demand projections.

The objective is not to optimise the system, but to test internal consistency:

- If announced capacity targets are delivered,
- and demand evolves along planning ranges,
- what happens to emissions?
- and what scale of investment is implied?

The model is intentionally simple and auditable.

---

## 1. Data

Historical generation data: Ember European electricity dataset (long-format CSV).

Policy anchors drawn from:

- Updated German NECP draft
- Offshore wind expansion law
- Coal phase-out legislation (latest end-date 2038)
- Climate neutrality objective (2045)

Capacity targets are specified at anchor years and linearly interpolated in between.

---

## 2. Model Structure

### 2.1 Capacity Pathway

Targets are defined for:

- Solar PV (GW)
- Onshore wind (GW)
- Offshore wind (GW)

Between anchor years:

$$
\text{Capacity}_{t} = \text{Interpolated between policy targets}
$$

---

### 2.2 Generation

Annual renewable generation:

$$
\text{Generation}_{i,t} = \text{Capacity}_{i,t} \times 8760 \times CF_i
$$

Where:

- \( CF_i \) = assumed capacity factor  
- 8760 = hours per year  

Capacity factors used:

- PV: 14%
- Onshore wind: 30%
- Offshore wind: 45%

Other clean generation (hydro, biomass, etc.) is held constant at baseline levels.

Coal generation declines linearly to zero by 2038.

Gas fills the residual:

$$
\text{Gas}_t = \max \left(0, \text{Demand}_t - \text{Clean}_t - \text{Coal}_t \right)
$$

Curtailment is explicitly computed when renewable output exceeds demand.

---

### 2.3 Emissions

$$
\text{Emissions}_t = E_{\text{coal}} \cdot \text{Coal}_t + E_{\text{gas}} \cdot \text{Gas}_t
$$

Emission factors (MtCO₂ per TWh):

- Coal: 0.95
- Gas: 0.40

Intensity:

$$
\text{Intensity}_t = \frac{\text{Emissions}_t}{\text{Demand}_t} \div 0.001
$$

(Conversion: \(1 \text{ g/kWh} = 0.001 \text{ Mt/TWh}\))

---

### 2.4 Investment Envelope

Annual CAPEX:

$$
\text{CAPEX}_{i,t} = \Delta \text{Capacity}_{i,t} \times 10^6 \times c_i
$$

Where:

- \( c_i \) = cost in €/kW  
- \( 10^6 \) converts GW to kW  

Indicative costs:

- PV: €700/kW  
- Onshore wind: €1300/kW  
- Offshore wind: €2500/kW  

Cumulative investment is tracked over time.

This reflects technology CAPEX only (no grid, storage, financing, or system costs).

---

## 3. What This Model Is — and Is Not

This is a policy-consistency check.

It evaluates whether announced capacity trajectories mathematically support decarbonisation objectives.

It does **not**:

- Optimise dispatch
- Model storage or hydrogen integration
- Include interconnector flows
- Discount investment flows
- Model market prices
- Capture grid expansion costs

Assumptions:

- Full delivery of announced capacity
- Fixed capacity factors
- Gas acts as unconstrained residual balancing source

---

## 4. Key Observations

Typical outcomes:

- Renewable build-out substantially increases system capacity by 2035–2045.
- Curtailment emerges at high penetration levels without storage.
- Gas remains structurally necessary for balancing.
- Emissions decline sharply, but near-zero requires more than capacity expansion alone.
- Investment requirements are front-loaded and scale with PV and offshore expansion.

---

## 5. How to Run

1. Upload the Ember CSV file.
2. Adjust policy anchor values if desired.
3. Run the notebook.
4. Review:
   - Emissions trajectory
   - Curtailment levels
   - Gas residual role
   - Annual and cumulative CAPEX

---

## 6. Extensions

Possible next steps:

- Add storage absorption
- Introduce declining gas emission factors (hydrogen blending)
- Include grid CAPEX proxy
- Apply discounted cash flow analysis
- Run demand sensitivity scenarios

---

## 7. Motivation

Energy transition debates often focus on targets.

This project converts targets into arithmetic.

It provides a clear, transparent answer to:

- Are capacity plans internally coherent?
- What emissions trajectory follows?
- What investment envelope is implied?
