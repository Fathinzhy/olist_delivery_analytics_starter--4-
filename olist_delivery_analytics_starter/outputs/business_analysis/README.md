# Notebook 03 — Output Guide

These files are produced by `notebooks/03_kpi_analysis_business_investigation.ipynb` from the validated analytical models.

- `executive_kpi_summary.csv`: KPI values, human-readable display values, definitions, populations and interpretations.
- `delivery_review_comparison.csv`: order-level review outcomes by delivery performance group.
- `monthly_delivery_performance.csv`: purchase-cohort trend, with explicit month coverage, maturity and sample-size flags.
- `state_delivery_performance.csv`: state-level late rates, low-rating shares, and observed excess late-order count relative to the overall rate.
- `category_delivery_performance.csv`: order-category exposures; an order may appear in more than one category.
- `seller_delivery_performance.csv`: order-seller exposures; this is not a measure of seller fault.
- `fulfillment_stage_summary.csv`: stage-duration medians, p90s and coverage.
- `analysis_quality_checks.csv`: required checks; all must be `PASS` before sharing the figures.
- `recommendation_candidates.csv`: descriptive investigation queue, not a causal ranking.
- `findings_summary.md`: generated narrative findings and limitations.

Rates in the CSVs are stored as fractions (e.g., `0.0677` means 6.77%) for machine use. `executive_kpi_summary.csv` also contains `display_value` for quick human reading.

These are historical results from the Olist dataset and should not be presented as current operating metrics. Rerun Notebook 03 after rerunning Notebooks 01 and 02 to regenerate the reports.
