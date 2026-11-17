# Data Quality Review — Olist Dataset

This review reflects the uploaded Olist ZIP and the checks in `notebooks/01_data_loading_cleaning.ipynb`. It is an audit of historical public data, not an assessment of Olist's current operations.

## Main checks

- **Row-count reconciliation:** all 9 source tables pass. Only exact duplicate geolocation rows were removed; all other source tables retain their row counts.
- **Candidate keys:** all 8 tested candidate keys pass uniqueness and non-null checks.
- **Geolocation lookup:** 19,015 non-null postal-code prefixes, exactly one row per prefix. Coordinates use the median; city/state labels use deterministic mode with lexicographical tie-breaking.
- **Join cardinality:** many-to-one left joins preserve all 99,441 customer rows and all 3,095 seller rows.
- **Reference-data exceptions:** 278 customer records and 7 seller records reference postal prefixes absent from the geolocation lookup; 13 product rows have a non-null category absent from the translation table; 610 product rows have missing category values.
- **Date sequence checks:** 1,359 orders have carrier handoff before approval; 23 have customer delivery before carrier handoff. These invalidate the affected stage-duration metrics, not necessarily all other metrics for those orders.
- **Numeric review queue:** zero, missing or negative candidates in selected non-negative fields are flagged for review, not automatically treated as errors.

## Recommended treatment

1. Keep source and cleaned tables distinct; never modify the original ZIP.
2. Use left joins for optional reference lookups and retain unmatched primary records.
3. Join through the geolocation prefix lookup rather than the large raw geolocation observations table.
4. For each KPI, exclude or flag only records invalid for that calculation and document both eligible population and exclusions.
5. Aggregate order items, payments and reviews separately before joining them to order grain.

## Follow-on modeling

`notebooks/02_build_analytical_model.ipynb` creates the one-row-per-order and one-row-per-order-item models, reconciles totals, and writes `outputs/analytical_model/model_validation_report.csv`. All structural model checks must pass before KPI analysis begins.
