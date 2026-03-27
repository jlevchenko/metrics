# Tiered Service Assignment Pipeline
## Federal School Mental Health Grant Programs — Year 2

Automated assignment of **technical assistance (TA) service tiers** to school mental health grantees across two federal grant programs. This pipeline replaces a manual review process with a reproducible, auditable Python workflow that scores grantees on performance and compliance dimensions, then writes tier assignments back into the official contact management file.

---

## Background

The METRICS TA Center supports grantees in two Department of Education school mental health programs:

- **MHSP** (Mental Health Service Professionals) — funds higher education institutions to train and place school-based mental health providers
- **SBMH** (School-Based Mental Health) — funds K–12 school agencies to hire, retain, and expand access to mental health services

Each year, grantees are assigned to one of three TA support tiers based on their Annual Performance Report (APR). Tier placement determines the frequency of coaching calls, the intensity of data review, and whether a grantee is flagged for additional federal oversight.

| Tier | Label | Description |
|------|-------|-------------|
| **1** | Universal | On-track grantees; standard TA support |
| **2** | Targeted | Some concerns; more frequent check-ins |
| **3** | Intensive | Significant issues; highest TA engagement and potential federal review |

---

## Repository Structure

```
tier_assignment/
├── tier_assignments.ipynb           # Main pipeline notebook
├── data/
│   ├── MHSP_2025_Cleaned.xlsx       # MHSP Year 2 APR data (anonymized)
│   ├── SBMH_2025_Cleaned.xlsx       # SBMH Year 2 APR data (anonymized)
│   └── METRICS_Contacts.xlsx        # Grantee contact file with FPO/coach assignments
└── output/
    ├── METRICS_Contacts_With_Tiers.xlsx   # Final contact file with tier assignments
    └── tier_assignments.log               # Run log
```

---

## Tiering Methodology

Each grantee receives two independently computed tiers. The **overall tier** is the more intensive of the two.

### Benchmark Tier

Based on weighted GPRA measure attainment. Each measure is scored 0/1 against a Year 2 threshold. Scores are combined into a weighted percent, then mapped to a tier.

**MHSP measures and weights:**

| Measure | Description | Threshold | Weight |
|---------|-------------|-----------|--------|
| GPRA 1a | Trained (annual) | actual ≥ 10% of target | 40% |
| GPRA 1b | Placed (current) | actual ≥ 10% of target | 20% |
| GPRA 2a | Internship (annual) | actual ≥ 10% of target | 10% |
| GPRA 2b | Internship (current) | actual ≥ 10% of target | 10% |
| GPRA 3a | Retained (annual) | actual ≥ 5% of target | 10% |
| GPRA 3b | Retained (current) | actual ≥ 5% of target | 10% |

**SBMH measures and weights:**

| Measure | Description | Threshold | Weight |
|---------|-------------|-----------|--------|
| GPRA 1 | Providers hired | actual ≥ 50% of target | 40% |
| GPRA 2 | Providers retained | actual ≥ 50% of target | 20% |
| GPRA 3 | Student-to-provider ratio | actual ratio ≤ target | 10% |
| GPRA 4 | Telehealth students served | actual ≥ 25% of target | 20% |
| GPRA 5 | Total students served | actual ≥ target | 10% |

**Benchmark tier cutoffs:**

| Weighted % Met | Tier |
|---|---|
| ≥ 75% | 1 |
| 50–74.9% | 2 |
| < 50% | 3 |

> Year 2 thresholds reflect early-grant-period expectations. Grantees are not expected to fully meet multi-year targets in the second reporting period.

### Revision Tier

Based on APR submission and revision completion status:

| Condition | Tier |
|---|---|
| `No Revisions Needed == 1` | 1 |
| `Revisions Needed == 1` AND `Revisions Incorporated == 1` | 2 |
| Revisions outstanding or status unclear | 2 |

### Missing APR Override

Grantees that did not submit an APR are automatically assigned **Tier 3**, overriding both benchmark and revision scores. Their grant IDs are listed in the `MISSING_APR_IDS` set in the notebook.

### Overall Tier

```
overall_tier = max(benchmark_tier, revision_tier)
```

### Merge Logic

Tier assignments are merged into the official contact sheet using `grant_id` → `Grant ID` as the key (left join from contact list). Any grantee in the contact file not found in the APR data defaults to **Tier 1**.

---

## Requirements

```bash
pip install pandas openpyxl numpy
```

Python 3.8+ recommended.

---

## Usage

1. Place input files in the `data/` directory (included in this repo as anonymized examples)
2. Open `tier_assignments.ipynb` in Jupyter
3. Update `DATA_DIR` in the Setup cell if needed
4. Run all cells — output is written to `output/METRICS_Contacts_With_Tiers.xlsx`

---

## Data Privacy

All data in this repository are **synthetic and anonymized**. Grantee names, grant IDs, FPO names, coach names, PI names, and all email addresses have been replaced with fictional values. No real individuals, institutions, or government personnel are identified. This project is for portfolio demonstration only.
