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
**What it does:** Loads the full UCMR5 dataset (1,928,117 rows across all contaminants) and filters it down to PFOA and PFOS measurements only. Drops three columns that are entirely empty in the UCMR5 dataset. Exports the cleaned PFOA/PFOS-only dataset for use in subsequent notebooks.

**Output:** `ucmr5_pfoa_pfos.csv`
- 128,400 PFOA/PFOS samples
- 8,648 detections above the reporting limit

---

### 2. `02_analysis.ipynb`
**What it does:** Builds an entry point-level summary by averaging PFOA and PFOS concentrations across all sample events per entry point (identified by PWSID + FacilityID). Removes one PWS (NV0000167) with a missing FacilityID. Merges with SDWIS population data to determine how many people are served by each entry point. Groups water systems into population size bins for cost estimation.

**Key assumption:** When a water system has multiple entry points, the system's total population is divided equally across them.

**Output:** `ep_table.csv`
- 26,438 unique entry points
- Includes average PFOA and PFOS concentration per EP, population served, and population size bin

---

### 3. `04_scaling.ipynb`
**What it does:** Scales UCMR5 findings up to national estimates. Filters SDWIS to active Community Water Systems (CWS) only and counts them by population bin to establish the national universe (49,369 systems total). Reads `ep_table.csv` to calculate exceedance percentages per population bin for each threshold. Applies the formula: National PWS count × average EPs per PWS (M) × exceedance rate per bin, summed across bins.

**Key assumptions:**
- Average EPs per PWS (M) = **2.5749** — derived from ep_table.csv (26,148 EPs / 10,155 PWS)
- Exceedance is defined as PFOA **or** PFOS exceeding the threshold at a given entry point
- National universe = active CWS from SDWIS, binned by population size

**Output:** `final_national_impact.csv`
- Estimated number of EPs requiring treatment per population bin, for each of the 5 thresholds (4, 8, 12, 20, 40 ng/L)

---

### 4. `03_cost_risk_treatment_analysis.ipynb`
**What it does:** Reads `ep_table.csv` and `final_national_impact.csv` to estimate national treatment costs and exposure reduction across five regulatory thresholds. Flags each entry point as exceeding each threshold (PFOA or PFOS). Calculates exposure per day (ng/day) per entry point assuming 2 L/day water intake. Maps national EP counts from the scaling table to annualized cost per EP (from Corona memo, by population bin). Also computes cost-effectiveness as $/ng of PFAS removed annually.

**Thresholds analyzed:** 4, 8, 12, 20, and 40 ng/L

**Key outputs:**
- National annualized treatment costs by threshold: $3,396M (4 ng/L) → $109M (40 ng/L)
- Annual exposure reduction estimates (ng/year) per threshold
- Cost-effectiveness ratio ($/ng of PFAS removed)

---

## Output Files Summary

| File | Produced By | Contents |
|---|---|---|
| `ucmr5_pfoa_pfos.csv` | `01_filter_clean.ipynb` | Cleaned PFOA/PFOS sample data |
| `ep_table.csv` | `02_analysis.ipynb` | Entry point-level summary with population |
| `final_national_impact.csv` | `04_scaling.ipynb` | National scaling estimates by threshold |
| Cost & exposure tables | `03_cost_risk_treatment_analysis.ipynb` | Treatment costs and exposure reduction |

---

## Dependencies

- Python 3.x
- pandas, numpy, matplotlib, seaborn, jupyter

---

*For questions about this analysis, please contact the project author.*
