# metrics

**APR Data Cleaning: Region Assignment and Raw Variable Extraction**

This repository contains data cleaning code for Annual Performance Report (APR) data
from two federal school mental health grant programs administered by the WCER School
Mental Health Collaborative: **MHSP** (Mental Health Service Professionals) and
**SBMH** (School-Based Mental Health).

The notebook prepares grantee-level program data for downstream analysis by standardizing
headers, resolving structural issues in the raw APR export, assigning geographic regions,
and extracting the GPRA outcome variables needed for reporting.

---

## Project Context

Federal grantees submit annual performance data through the APR system. The raw export
is a two-sheet Excel workbook with a non-standard header structure, occasional duplicate
column names, and formatting rows that must be removed before analysis. This notebook
handles all of those issues and produces clean, analysis-ready files for each program.

**Programs covered:**

- **MHSP** -- Mental Health Service Professionals program (~158 grantees)
- **SBMH** -- School-Based Mental Health program (~120 grantees)

**GPRA measures extracted:**

| Program | Measures |
|---------|----------|
| MHSP | GPRA 1A, 1B, 2A, 2B, 3A, 3B, 4 (raw counts + ratios) |
| SBMH | GPRA 1-6 (raw counts; GPRA 3 as ratio) |

---

## Repository Structure

```
metrics/
├── APR_Region_and_Raw_Variables.Rmd   # Main cleaning notebook
├── APR_Region_and_Raw_Variables.nb.html  # Rendered notebook output
├── data/
│   ├── APR_Application_Data_Synthetic.xlsx  # Synthetic sample data (mirrors real structure)
│   ├── mhsp_cleaned.xlsx     # Full cleaned MHSP output
│   ├── mhsp_raw.xlsx         # MHSP raw-variable subset
│   ├── sbmh_cleaned.xlsx     # Full cleaned SBMH output
│   └── sbmh_raw.xlsx         # SBMH raw-variable subset
└── README.md
```

> **Note on data:** Real grantee data is not included in this repository due to
> data use agreements. The `data/` folder contains a fully synthetic dataset that
> mirrors the structure, column names, and data types of the actual APR export.
> All grantee names, IDs, and identifying information in the synthetic file are
> fabricated.

---

## Cleaning Steps

### 1. Import and Header Standardization

The APR workbook uses a two-row header: row 1 is a metadata banner, row 2 contains
column names, and row 3 is a separator artifact. The notebook promotes row 2 to
column names and drops all three header rows before any analysis.

### 2. Duplicate Column Resolution

The APR export occasionally produces identically named columns when a GPRA measure
appears in both an annual and a current-year block. A reusable function detects
duplicated names, checks whether the two versions carry identical values, renames
them with `_1` / `_2` suffixes for inspection, and logs whether values differ.
Crosscheck queries confirm which copy to retain before the redundant column is dropped.

### 3. Duplicate Grantee Check

Each grantee should appear exactly once per program sheet. A grouped filter surfaces
any grantee-level duplicates for manual review.

### 4. Geographic Region Assignment

Grantees are assigned to one of four broad regions -- West, Central, Northeast,
Southeast -- based on their reported state abbreviation. The assignment uses a
vectorized `case_when()` function rather than a loop, and any unmatched state codes
are flagged for review before the file is exported.

| Region | States |
|--------|--------|
| West | AK, AZ, CA, CO, HI, ID, MT, NM, NV, OR, UT, WA, WY |
| Central | IA, IL, IN, KS, MI, MN, MO, ND, NE, OK, SD, WI |
| Northeast | CT, DC, DE, KY, MA, MD, ME, NH, NJ, NY, OH, PA, RI, VA, VT, WV |
| Southeast | AL, AR, FL, GA, LA, MS, NC, PR, SC, TN, TX |

### 5. Raw Variable Extraction and Export

A curated list of GPRA raw-count and ratio variables (targets and actuals) is
extracted from each program's cleaned dataframe and saved to a separate Excel file
for use in performance reporting.

---

## Usage

1. Clone this repository
2. Place your APR workbook in `data/` and update the file path in the import chunks
3. Open `APR_Region_and_Raw_Variables.Rmd` in RStudio
4. Run all chunks or knit to HTML notebook

**R packages required:** `readxl`, `tidyverse`, `writexl`

```r
install.packages(c("readxl", "tidyverse", "writexl"))
```

---

## Notes

- The `resolve_duplicate_columns()` function (MHSP section 2.2) is written to handle
  exactly two copies of a duplicated name and will warn if more are found.
- The `assign_region()` function uses `dplyr::case_when()` and returns `"Unknown"` for
  any unrecognized state code. The SBMH section explicitly checks for unknowns and
  prints the affected rows before export.
- `intersect()` is used when subsetting raw-variable columns so the notebook does not
  error if a column is missing from a particular year's export.

---

## Related Repositories

This notebook is part of a broader portfolio of data pipelines developed for the
WCER School Mental Health Collaborative. See also:

- [`gpra-scoring`](https://github.com/jlevchenko) -- GPRA Year 2/3 scoring pipeline
- [`roi-analysis`](https://github.com/jlevchenko) -- ROI longitudinal analysis
- [`progress-scoring`](https://github.com/jlevchenko) -- Substantial progress scoring pipeline
- [`ta-assignment`](https://github.com/jlevchenko) -- Tiered TA assignment pipeline
