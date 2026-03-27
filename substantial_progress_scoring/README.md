# Substantial Progress Scoring Pipeline
## Federal School Mental Health Grant Programs — Year 1 APR Automation

Automated scoring and file management for **Substantial Progress** determinations across two federal school mental health grant programs. This pipeline replaces a fully manual FPO workflow — previously requiring officers to hand-enter 0/1 scores into hundreds of individual Excel workbooks — with a reproducible, auditable Python process.

---

## Background

The U.S. Department of Education requires each school mental health grantee to demonstrate **Substantial Progress** in their Annual Performance Report (APR). Federal Program Officers (FPOs) evaluate each grantee across two dimensions:

1. **Administrative factors** — financial management (LAB/drawdown rates), staffing stability, internal controls, grant conditions
2. **GPRA performance** — whether the grantee met Year 1 thresholds on each federally-mandated GPRA measure

FPOs completed this review using program-specific Excel workbooks, one sheet per grantee, with scores entered manually. As portfolio sizes grew (50–150+ grantees per FPO), automating the GPRA scoring step became critical for consistency and efficiency.

---

## Programs

| Program | Full Name | Grantee Type |
|---------|-----------|--------------|
| **MHSP** | Mental Health Service Professionals | Higher education institutions |
| **SBMH** | School-Based Mental Health | K–12 education agencies |

---

## Repository Structure

```
substantial_progress_scoring/
├── substantial_progress_scoring.ipynb   # Main pipeline notebook
├── requirements.txt                     # Python dependencies
├── data/
│   ├── fpo_workbooks/                   # Input: multi-sheet FPO workbooks (one per FPO)
│   │   ├── SP_Workbook_SBMH_FPO_A.xlsx
│   │   ├── SP_Workbook_SBMH_FPO_B.xlsx
│   │   ├── SP_Workbook_MHSP_FPO_C.xlsx
│   │   ├── SP_Workbook_MHSP_FPO_E.xlsx
│   │   ├── APR_Review_Checklist_FPO_D.xlsx
│   │   └── ...
│   ├── split_workbooks/                 # Output: per-grantee split files (generated)
│   ├── FINAL_Year2_MHSP_APR.xlsx        # MHSP APR Smartsheet export (scoring source)
│   └── FINAL_Year2_SBMH_APR.xlsx        # SBMH APR Smartsheet export (scoring source)
├── output/
│   ├── mhsp_scores.json                 # Computed GPRA scores — MHSP grantees
│   ├── sbmh_scores.json                 # Computed GPRA scores — SBMH grantees
│   └── update_log.txt                   # Record of all file modifications
└── templates/                           # Reference workbook templates
    ├── SP_Workbook_SBMH_Template.xlsx
    ├── SP_Workbook_SBMH_Single_Grantee_Example.xlsx
    ├── SP_Workbook_MHSP_Single_Grantee_Example.xlsx
    └── SP_Workbook_MHSP_Single_Grantee_Example_2.xlsx
```

---

## Pipeline Overview

### Step 1 — Split FPO Workbooks
Each FPO submits a multi-sheet Excel workbook with one tab per assigned grantee. The pipeline iterates through all workbooks in `data/fpo_workbooks/`, extracts sheets whose names match a grant ID pattern, and saves each as an individual file in `data/split_workbooks/`. All cell formatting, column widths, row heights, and merged cells are preserved using `openpyxl`.

### Step 2 — Score GPRA Measures
APR data from the MHSP and SBMH Smartsheet exports are loaded and standardized. Two program-specific scoring functions apply Year 1 GPRA thresholds and return binary (0/1) scores per measure. Results are stored as structured JSON.

**MHSP Year 1 thresholds:**

| Measure | Description | Threshold |
|---------|-------------|-----------|
| GPRA 1a | Trained annually | actual ≥ 0% of target |
| GPRA 1b | Placed currently | actual ≥ 0% of target |
| GPRA 2a | Internship (annual) | actual ≥ 5% of target |
| GPRA 2b | Internship (current) | actual ≥ 5% of target |
| GPRA 3a | Retained (annual) | actual ≥ 0% of target |
| GPRA 3b | Retained (current) | actual ≥ 0% of target |

**SBMH Year 1 thresholds:**

| Measure | Description | Threshold |
|---------|-------------|-----------|
| GPRA 1 | Providers hired | actual ≥ 20% of target |
| GPRA 2 | Providers retained | actual ≥ 20% of target |
| GPRA 3 | Student-to-provider ratio | actual ratio ≤ target ratio |
| GPRA 4 | Telehealth students served | actual ≥ 11% of target |
| GPRA 5 | Total students served | actual ≥ target |

> Year 1 thresholds are intentionally lenient — grantees are not expected to fully meet multi-year targets in their first reporting period.

### Step 3 — Insert Scores into Grantee Files
The pipeline matches each split grantee file to its computed scores by grant ID, then writes 0/1 values into designated cells. MHSP files also receive two new rows (GPRA 3a/3b) with updated weighted-average formulas. SBMH files receive formula sign corrections and number formatting updates. All changes are logged to `output/update_log.txt`.

---

## Score Format

```json
{
  "S184H99005001": {
    "GPRA1": 1,
    "GPRA2": 1,
    "GPRA3": 0,
    "GPRA4": 1,
    "GPRA5": 1
  },
  ...
}
```

---

## Requirements

```bash
pip install -r requirements.txt
```

Core dependencies: `pandas`, `openpyxl`, `numpy`. See `requirements.txt` for the full pinned environment used during development.

---

## Usage

1. Place FPO workbooks in `data/fpo_workbooks/`
2. Place APR source files in `data/` (see filenames above)
3. Open `substantial_progress_scoring.ipynb` in Jupyter
4. Update `DATA_DIR`, `INPUT_DIR`, and `OUTPUT_DIR` in the Setup cell if needed
5. Run all cells top-to-bottom

Output files are written to `data/split_workbooks/` and `output/`.

---

## Data Privacy

All data in this repository are **synthetic and anonymized**. Grant IDs, grantee names, institution names, and reviewer notes have been replaced with fictional values. No real FPO names, personnel information, or identifiable grantee data are included. This project is intended for portfolio demonstration purposes only.
