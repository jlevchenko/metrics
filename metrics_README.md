# metrics

**Data Pipelines Supporting METRICS -- the Mental Health Evaluation, Training,
Research, and Innovation Center for Schools**

This repository contains the data processing and reporting pipelines used to
support METRICS, the national Mental Health Personnel Technical Assistance
Center housed at the Wisconsin Center for Education Research (WCER),
UW-Madison. It covers region assignment for ad hoc requests, annual and
interim performance reports, return on investment analysis, substantial
progress scoring, and tier assignment for the grantees METRICS supports.

---

## Program Background

METRICS was awarded to UW-Madison by the U.S. Department of Education in
September 2023 as a four-year, $10.4 million federal contract to build and
run a national center focused on expanding and improving the school-based
mental health workforce (school psychologists, social workers, and other
practitioners) across the country. The center is led by Stephen Kilgus,
Katie Eklund, and Andy Garbacz, co-directors of the School Mental Health
Collaborative (SMHC) in the Department of Educational Psychology, with
Kilgus serving as executive director. Partner institutions include the
University of South Florida, the University of Iowa, and UC Santa Barbara.

METRICS provides training, resources, and data support to nearly 300 grants
that the Department of Education has awarded to state education agencies,
school districts, and universities to grow and diversify the mental health
workforce, especially in high-need districts. Those grantee awards
collectively represent a federal investment of more than $188 million. The
center's core functions include:

- Building a pipeline of school-based mental health providers from diverse backgrounds and the communities they serve
- Hosting communities of practice where grantees share resources and implementation experience
- Providing training on grantee data reporting and analyzing aggregated grantee data to surface successes, challenges, and trends
- Disseminating resources, best practices, and evidence-based tools through the METRICS website and social media
- Running webinars and meetings on program evaluation and continuous improvement

A central piece of that mission is collecting high-quality outcome data from
grantees so federal officials can evaluate return on investment. This
repository implements the data-side work behind that mission: cleaning and
aggregating the performance data grantees report, assigning grantees to
regions and performance tiers, scoring substantial progress, and producing
the annual and interim reports submitted to the Department of Education.

*Source: [UW-Madison to lead new national center addressing lack of school mental health providers](https://education.wisc.edu/news/uw-madison-to-lead-new-national-center-addressing-lack-of-school-mental-health-providers/), WCER Communications, October 5, 2023.*

---

## Repository Structure

```
metrics/
├── ad_hoc_requests/
│   └── region_assignment/          # One-off region assignment requests and lookups
├── annual_performance_reports/     # APR data cleaning and reporting pipeline
├── interim_performance_reports/    # IPR data aggregation and GPRA tier assignment
├── return_on_investment_analysis/  # ROI analysis for grantee outcomes vs. federal investment
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
the nearly 300 Department of Education grantees METRICS supports, and
produces the standardized outputs used for federal reporting.

### `interim_performance_reports`

Aggregates Interim Performance Report (IPR) data and assigns GPRA
(Government Performance and Results Act) tiers based on grantee progress
against program benchmarks.

### `return_on_investment_analysis`

Computes return on investment metrics tying grantee outcomes back to the
federal investment in each award, supporting METRICS's mandate to help the
Department of Education evaluate the return on its grant funding.

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

- This README reflects the current top-level folder structure and public
  program background. If you'd like more detail per pipeline (specific
  scripts, package requirements, input/output file naming conventions),
  share the contents of each subfolder and I can flesh out the
  corresponding sections.

---

## Related Repositories

- [`rep`](https://github.com/jlevchenko) -- REP 3.1 component analysis (fidelity, descriptives, outcome models)
- [`rep-4.1`](https://github.com/jlevchenko) -- REP 4.1 outcome analysis (multiple imputation, ANCOVA, robust SEs)
- [`ipr-registration`](https://github.com/jlevchenko) -- IPR webinar registrant matching pipeline
- [`power-analysis`](https://github.com/jlevchenko) -- NIMH grant power analysis
