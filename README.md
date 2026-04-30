# PFAS Drinking Water Contamination Analysis
### U.S. PFOA & PFOS Exposure and Treatment Cost Estimates Using EPA UCMR5 Data

## What This Project Does

This project analyzes PFOA and PFOS contamination in U.S. public drinking water using the EPA's most recent national monitoring data. It estimates how many Americans may be exposed above various regulatory thresholds and what it would cost to treat affected water systems nationally.

## Background

PFOA and PFOS are per- and polyfluoroalkyl substances (PFAS), synthetic chemicals linked to cancer, immune suppression, and other health harms. They persist in the environment and accumulate in the body, which is why they are called "forever chemicals."

The EPA's Unregulated Contaminant Monitoring Rule 5 (UCMR5) required public water systems across the U.S. to test for PFAS from 2021 to 2023, producing the most comprehensive national dataset on PFAS in drinking water to date.

## Data Sources

| Source | What It Contains | How It's Used |
|---|---|---|
| **EPA UCMR5** | PFAS measurements at public water systems nationwide | Primary contamination data |
| **EPA SDWIS** | National inventory of all public water systems and population served | Used to scale UCMR5 sample to national estimates |
| **Black & Veatch / Corona (2024)** | Treatment cost estimates by system size | Used to estimate national treatment costs |

**Primary data citation:**
> U.S. Environmental Protection Agency. (January 2026). *The Fifth Unregulated Contaminant Monitoring Rule (UCMR 5) Data Summary: January 2026.* Office of Water (MS-140). EPA 815-S-26-001.

The January 2026 release represents approximately 95% of total expected results. Data are publicly available via the [UCMR 5 Data Finder](https://www.epa.gov/dwucmr/occurrence-data-unregulated-contaminant-monitoring-rule).

## Repository Structure

```
project/
├── current_work/          # Active analysis (entry point level) ← use this
│   ├── 01_filter_clean.ipynb
│   ├── 02_analysis.ipynb
│   ├── 03_cost_risk_treatment_analysis.ipynb
│   └── 04_scaling.ipynb
└── archive/               # Superseded work, kept for reference only
    ├── (PWSID-level analysis)
    └── (old scaling method)
```

## Notebooks

All notebooks are in the `current_work/` folder. Run them in order: 01, 02, 04, then 03.

### 1. `01_filter_clean.ipynb`

Loads the full UCMR5 dataset (1,928,117 rows) and filters to PFOA and PFOS only. Drops three columns that are entirely empty. Exports the cleaned dataset for use in the next notebook.

Output: `ucmr5_pfoa_pfos.csv` with 128,400 samples and 8,648 detections.

### 2. `02_analysis.ipynb`

Averages PFOA and PFOS concentrations per entry point (PWSID + FacilityID). Removes one PWS (NV0000167) with a missing FacilityID. Merges with SDWIS population data and assigns each entry point to a population size bin.

Population is divided equally across entry points for systems with multiple entry points.

Output: `ep_table.csv` with 26,438 unique entry points including average concentrations, population served, and population bin.

### 3. `04_scaling.ipynb`

Scales UCMR5 findings to national estimates. Filters SDWIS to active Community Water Systems and counts them by population bin (49,369 systems total). Calculates the percent of entry points exceeding each threshold per bin, then applies: National PWS count x average EPs per PWS (2.5749) x exceedance rate per bin.

Output: `final_national_impact.csv` with estimated EPs requiring treatment per bin for thresholds of 4, 8, 12, 20, and 40 ng/L.

### 4. `03_cost_risk_treatment_analysis.ipynb`

Uses `ep_table.csv` and `final_national_impact.csv` to estimate national treatment costs and exposure reduction. Assumes 2 L/day water intake for exposure calculations. Maps national EP counts to annualized cost per EP from the Corona memo. Also calculates cost-effectiveness as dollars per nanogram of PFAS removed.

Thresholds: 4, 8, 12, 20, and 40 ng/L.

Key results: National annualized costs range from $3,396M at 4 ng/L down to $109M at 40 ng/L.

## Output Files

| File | Produced By | Contents |
|---|---|---|
| `ucmr5_pfoa_pfos.csv` | `01_filter_clean.ipynb` | Cleaned PFOA/PFOS sample data |
| `ep_table.csv` | `02_analysis.ipynb` | Entry point summary with population |
| `final_national_impact.csv` | `04_scaling.ipynb` | National scaling estimates by threshold |
| Cost and exposure tables | `03_cost_risk_treatment_analysis.ipynb` | Treatment costs and exposure reduction |

## Dependencies

- Python 3.11
- pandas, numpy, matplotlib, seaborn, jupyter, openpyxl, scipy

Use `environment.yml` to set up the conda environment:

```bash
conda env create -f environment.yml
conda activate pfas_analysis
```
