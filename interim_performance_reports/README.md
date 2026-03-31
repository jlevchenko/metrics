# interim-performance-reports

**IPR Data Aggregation and GPRA Benchmark Tier Assignment**

This repository contains R notebooks that process Interim Performance Report (IPR)
data for two federal school mental health grant programs administered by the WCER
School Mental Health Collaborative: **MHSP** (Mental Health Service Professionals)
and **SBMH** (School-Based Mental Health).

The notebooks compute program-wide GPRA outcome totals, flag grantees with missing
or problematic data, produce narrative report paragraphs, and assign GPRA-based
benchmark tiers used for targeted technical assistance.

---

## Programs and Data

**MHSP** (~160 grantees, GPRA measures 1A, 1B, 2A, 2B, 3A, 3B)

| GPRA | Description |
|------|-------------|
| 1A/1B | Providers completing / enrolled in training |
| 2A/2B | Providers in / completing school placement |
| 3A/3B | Providers hired / retained in high-need LEAs |

**SBMH** (~104 grantees, GPRA measures 1-5)

| GPRA | Description |
|------|-------------|
| 1 | Providers hired |
| 2 | Provider retention |
| 3 | Student-to-provider ratio (reported as ratio string) |
| 4 | Provider attrition |
| 5 | Students receiving school-based mental health services |

---

## Repository Structure

```
interim-performance-reports/
├── 01_IPR_Aggregated_Data.Rmd          # Raw GPRA aggregation and cleaning
├── 02_IPR_GPRA_Benchmark_1Threshold.Rmd  # Benchmark tiers (1-GPRA threshold)
├── 03_IPR_GPRA_Benchmark_2Threshold.Rmd  # Benchmark tiers (2-GPRA threshold)
├── R/
│   └── helpers.R                       # Shared utility functions
├── data/
│   ├── REVISED_MHSP_IPR_Synthetic.xlsx      # Synthetic MHSP IPR data
│   ├── REVISED_SBMH_IPR_Synthetic.xlsx      # Synthetic SBMH IPR data
│   ├── APRs_Revised_Tiers_Synthetic.xlsx    # Synthetic APR grantee roster
└── README.md
```

> **Note on data:** Real grantee data are not included due to data use agreements
> and PII considerations. The `data/` folder contains fully synthetic files that
> mirror the column structure, data types, and row counts of the actual IPR exports.
> All names, email addresses, and grant numbers are fabricated.

---

## Notebooks

### `01_IPR_Aggregated_Data.Rmd`

The primary data cleaning and aggregation notebook. Runs for both programs.

1. Import each IPR Excel sheet using a shared `import_ipr_sheet()` helper that
   handles the non-standard two-row header, drops the separator row, and removes
   trailing all-NA columns.
2. Detect and remove duplicate Grant IDs, logging each case.
3. Cross-check grant IDs against the APR reference roster; report mismatches in
   both directions.
4. Clean all GPRA columns in one pass: rename from verbose Excel labels to
   snake_case, coerce to numeric, recode `999` sentinel values to NA.
5. Compute n, sum, mean, min, max for each GPRA measure.
6. List grantees with missing values for each measure.
7. Generate narrative "Additional Information" paragraphs with computed statistics.
8. Export a GPRA summary table and a revision follow-up Excel file.

### `02_IPR_GPRA_Benchmark_1Threshold.Rmd`

Assigns benchmark tiers using the **1-GPRA threshold rule**: a grantee is elevated
to a higher tier if any single GPRA measure falls below the cutoff.

### `03_IPR_GPRA_Benchmark_2Threshold.Rmd`

Assigns benchmark tiers using the **2-GPRA threshold rule**: a grantee is elevated
only when at least 2 GPRA measures fall below the cutoff. This is the more
conservative classification used to identify grantees for priority TA.

Both tier notebooks also assign a revision tier (Tier 1/2/3) based on whether the
grantee needed follow-up, submitted revisions, or received only a confirmation email.

---

## Benchmark Tier Definitions

| Tier | GPRA criterion | Meaning |
|------|---------------|---------|
| Tier 1 | actual >= 75% of target | On track |
| Tier 2 | 50-74% of target | Needs attention |
| Tier 3 | < 50% of target, or missing | Priority support |

GPRA 3 (SBMH) is a student-to-provider ratio string. It is converted to a decimal
(numerator / denominator) before tier comparison. For aggregation, all numerators
and denominators are summed separately to produce a program-wide ratio.

---

## Shared Helpers (`R/helpers.R`)

All three notebooks source a shared `helpers.R` file rather than repeating
functions inline. Key functions:

| Function | Purpose |
|----------|---------|
| `import_ipr_sheet()` | Import + standardize headers |
| `deduplicate_grants()` | Remove duplicate Grant IDs with logging |
| `crosscheck_grants()` | Report mismatches between IPR and APR rosters |
| `clean_gpra_col()` | Rename + numeric coerce + 999 recode |
| `summarise_gpra_col()` | n, sum, mean, min, max |
| `missing_grantees()` | List grantees with NA for a given measure |
| `parse_ratio()` / `aggregate_ratios()` | Parse GPRA 3 ratio strings |
| `ratio_to_decimal()` | Convert ratio string to decimal for tier comparison |
| `revision_summary()` | Count follow-up and revision submission by program |
| `standardize_names()` | Snake_case column name standardization |
| `benchmark_tier()` | Assign Tier 1/2/3 for one actual/target pair |
| `assign_overall_tier()` | Overall tier across GPRA measures (n-threshold) |

---

## Key Improvements Over Original Notebooks

The original notebooks used a per-column copy-paste pattern (rename, convert,
recode, sum, print) repeated ~20 times per notebook across three files. This
version consolidates:

- All repeated cleaning steps into `clean_gpra_col()`, applied in a single
  chained pipe at the top of each notebook
- All repeated ratio parsing into a unified `parse_ratio()` / `aggregate_ratios()`
  pair with robust handling of every format seen in the real data
- Both threshold variants of `assign_overall_tier()` now use the same function
  with a `tiers_required` argument rather than duplicated code
- The `condense_vars()` string-replacement chain replaced by a single
  `standardize_names()` function using regex
- Copy-paste comment errors fixed (e.g., frequency table comments referencing
  the wrong column name)

---

## Usage

1. Clone the repository
2. Replace the synthetic files in `data/` with your actual IPR exports, keeping
   the same column names and sheet structure
3. Open any `.Rmd` file in RStudio and knit or run chunks interactively

**R packages required:** `readxl`, `tidyverse`, `writexl`, `knitr`

```r
install.packages(c("readxl", "tidyverse", "writexl", "knitr"))
```

---

## Related Repositories

- [`metrics`](https://github.com/jlevchenko) -- APR region assignment and raw variable extraction
- [`ipr-registration`](https://github.com/jlevchenko) -- IPR webinar registrant matching pipeline
- [`gpra-scoring`](https://github.com/jlevchenko) -- GPRA Year 2/3 scoring pipeline
- [`ta-assignment`](https://github.com/jlevchenko) -- Tiered TA assignment pipeline
