# metrics

**Technical Assistance Program Metrics -- GPRA Reporting, Region Assignment, and Progress Scoring**

This repository contains the data processing and reporting pipelines used to
support federal technical assistance (TA) program metrics reporting. It
covers region assignment for ad hoc requests, annual and interim performance
reports, return on investment analysis, substantial progress scoring, and
tier assignment.

---

## Repository Structure

```
metrics/
├── ad_hoc_requests/
│   └── region_assignment/          # One-off region assignment requests and lookups
├── annual_performance_reports/     # APR data cleaning and reporting pipeline
├── interim_performance_reports/    # IPR data aggregation and GPRA tier assignment
├── return_on_investment_analysis/  # ROI analysis for TA program outcomes
├── substantial_progress_scoring/   # Substantial progress scoring logic and outputs
├── tier_assignment/                # Grantee tier assignment based on performance metrics
└── README.md
```

Each top-level folder is a self-contained pipeline with its own inputs,
processing scripts, and outputs. `data/temp` and `output/temp` (or `test`)
subfolders are working scratch space and are not tracked; they're cleared
out periodically rather than versioned.

---

## Folders

### `ad_hoc_requests/region_assignment`

Handles one-off requests to assign grantees, sites, or records to their
correct federal region. Used when region assignment is needed outside the
regular APR/IPR cycle (e.g., a special data pull or a one-time crosswalk
request).

### `annual_performance_reports`

Cleans and processes raw Annual Performance Report (APR) data submitted by
grantees, and produces the standardized outputs used for federal reporting.

### `interim_performance_reports`

Aggregates Interim Performance Report (IPR) data and assigns GPRA
(Government Performance and Results Act) tiers based on grantee progress
against program benchmarks.

### `return_on_investment_analysis`

Computes return on investment metrics for the TA program, linking program
inputs (cost, staff time, resources) to grantee outcomes.

### `substantial_progress_scoring`

Scores grantees on substantial progress criteria used in federal grant
monitoring, producing the documentation needed to support progress
determinations.

### `tier_assignment`

Assigns grantees to performance tiers based on a combination of APR/IPR
metrics, used to prioritize TA support and identify grantees needing
additional assistance.

---

## Usage

1. Clone the repository
2. Each subfolder's scripts expect raw grantee-submitted data in its local
   `data/` directory; update file paths as needed to point to your local
   copies (raw grantee data is not included due to data use restrictions)
3. Run each pipeline's scripts in order; outputs are written to that
   subfolder's `output/` directory

---

## Notes

- This README reflects the current top-level folder structure. If you'd
  like more detail per pipeline (specific scripts, package requirements,
  input/output file naming conventions), share the contents of each
  subfolder and I can flesh out the corresponding sections.

---

## Related Repositories

- [`rep`](https://github.com/jlevchenko) -- REP 3.1 component analysis (fidelity, descriptives, outcome models)
- [`rep-4.1`](https://github.com/jlevchenko) -- REP 4.1 outcome analysis (multiple imputation, ANCOVA, robust SEs)
- [`ipr-registration`](https://github.com/jlevchenko) -- IPR webinar registrant matching pipeline
- [`power-analysis`](https://github.com/jlevchenko) -- NIMH grant power analysis
