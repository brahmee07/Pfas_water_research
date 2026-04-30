# PFAS Drinking Water Contamination Analysis
### U.S. PFOA & PFOS Exposure and Treatment Cost Estimates Using EPA UCMR5 Data

---

## What This Project Does

This project analyzes contamination of U.S. public drinking water by two "forever chemicals" — **PFOA** and **PFOS** — using the EPA's most recent national monitoring data. It estimates how many Americans may be exposed above various regulatory thresholds, and what it would cost to treat affected water systems nationally.

---

## Background

**PFOA and PFOS** are per- and polyfluoroalkyl substances (PFAS) — synthetic chemicals linked to cancer, immune suppression, and other health harms. They persist in the environment and accumulate in the body, earning the nickname "forever chemicals."

The **EPA's Unregulated Contaminant Monitoring Rule 5 (UCMR5)** required public water systems across the U.S. to test for PFAS from 2021–2023, producing the most comprehensive national dataset on PFAS in drinking water to date.

---

## Data Sources

| Source | What It Contains | How It's Used |
|---|---|---|
| **EPA UCMR5** | PFAS measurements at public water systems nationwide | Primary contamination data |
| **EPA SDWIS** | National inventory of all public water systems and population served | Used to scale UCMR5 sample to national estimates |
| **Black & Veatch / Corona (2024)** | Treatment cost estimates by system size | Used to estimate national treatment costs |

**Primary data citation:**
> U.S. Environmental Protection Agency. (January 2026). *The Fifth Unregulated Contaminant Monitoring Rule (UCMR 5) Data Summary: January 2026.* Office of Water (MS-140). EPA 815-S-26-001.

The January 2026 release represents approximately 95% of total expected results, covering 29 PFAS and lithium across community water systems (CWSs) and non-transient non-community water systems (NTNCWSs) nationwide. Data are publicly available via the [UCMR 5 Data Finder](https://www.epa.gov/dwucmr/occurrence-data-unregulated-contaminant-monitoring-rule).

---

## Repository Structure

```
project/
├── current_work/          # Active analysis (entry point level) ← use this
│   ├── 01_filter_clean.ipynb
│   ├── 02_analysis.ipynb
│   ├── 03_cost_risk_treatment_analysis.ipynb
│   └── 04_scaling.ipynb
└── archive/               # Superseded work — kept for reference only
    ├── (PWSID-level analysis)
    └── (old scaling method)
```

---

## File & Notebook Overview

All active notebooks are in the `current_work/` folder. The analysis runs sequentially — each notebook takes the previous notebook's output as input.

---

### 1. `01_filter_clean.ipynb`
**What it does:** Loads the raw UCMR5 dataset and filters it down to PFOA and PFOS measurements only. Removes incomplete or unusable records and standardizes the data for analysis.

**Output:** `ucmr5_pfoa_pfos.csv`
- 121,260 PFOA/PFOS samples
- 8,167 detections above the reporting limit
- 26,148 unique entry points (EPs) — the individual sampling locations within water systems

---

### 2. `02_analysis.ipynb`
**What it does:** Summarizes contamination at the **entry point level** — the point where treated water enters the distribution system. Merges contamination data with SDWIS population data to determine how many people are served by each entry point. Groups water systems into population size bins for cost estimation.

**Key assumption:** When a water system has multiple entry points, the system's total population is divided equally across them.

**Output:** `ep_table.csv`
- Entry point-level summary table
- Includes population served, detected concentrations, and population size bin

---

### 3. `04_scaling.ipynb`
**What it does:** Scales findings from the UCMR5 sample up to **national estimates**. Because UCMR5 only monitored a subset of all U.S. water systems, this step uses SDWIS national counts and known relationships between system size and entry points to estimate how many entry points nationally would exceed each regulatory threshold.

**Key assumptions:**
- On average, each public water system has **2.57 entry points** (derived from UCMR5 data)
- UCMR5 exceedance rates are applied to the full national inventory of water systems from SDWIS
- National exposure reduction = *average reduction per entry point* (from UCMR5) × *estimated number of entry points nationally that would require treatment*

**Output:** `final_national_impact.csv`
- National scaling estimates by regulatory threshold
- Estimated number of entry points and people affected at each threshold

---

### 4. `03_cost_risk_treatment_analysis.ipynb`
**What it does:** Estimates the **national cost of treatment** and **reduction in human exposure** (measured in nanograms per day, ng/day) if water systems were required to treat PFOA/PFOS down to five different regulatory thresholds.

**Thresholds analyzed:** 4, 8, 12, 20, and 40 ng/L

**Outputs:**
- Treatment cost estimates by population size bin and threshold
- National exposure reduction estimates (ng/day) per threshold

---

## Output Files Summary

| File | Produced By | Contents |
|---|---|---|
| `ucmr5_pfoa_pfos.csv` | `01_filter_clean.ipynb` | Cleaned PFOA/PFOS sample data |
| `ep_table.csv` | `02_analysis.ipynb` | Entry point-level summary with population |
| `final_national_impact.csv` | `04_scaling.ipynb` | National scaling estimates by threshold |
| Cost & exposure tables | `03_cost_risk_treatment_analysis.ipynb` | Treatment costs and exposure reduction |

---

## Key Methods Notes

- **Entry point (EP):** The physical location where water enters a distribution system. One water system may have multiple EPs.
- **Detection vs. exceedance:** A "detection" means PFOA or PFOS was measurably present. An "exceedance" means the level was above a specific regulatory threshold.
- **Scaling approach:** UCMR5 monitored a nationally representative but incomplete sample of water systems. National estimates are produced by applying UCMR5 detection and exceedance rates to the full SDWIS national inventory.
- **Exposure reduction:** Estimated as the average concentration reduction achievable per entry point (from UCMR5 data) multiplied by the estimated number of entry points nationally that would need treatment at each threshold.

---

## Dependencies

- Python 3.x
- pandas, numpy, matplotlib, seaborn, jupyter

---


