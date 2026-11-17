# Olist Delivery Analytics — Data Quality & Analytical Modeling

This portfolio project analyzes the historical Olist Brazilian E-Commerce Public Dataset to investigate delivery reliability and customer satisfaction. The work is structured as a reproducible pipeline: source audit → clean extracts → validated analytical model → KPI analysis → business-facing dashboard and executive summary.

## Project notebooks

### Notebook 01 — `notebooks/01_data_loading_cleaning.ipynb`

Loads the nine CSV tables from the source ZIP, profiles data quality, applies conservative normalization, creates row-level exception reports, builds a one-row-per-postal-prefix geolocation lookup, and runs quality gates. It does **not** delete business records merely because values are missing, unusual or suspicious. Exact duplicate geolocation observations are removed and reconciled.

### Notebook 02 — `notebooks/02_build_analytical_model.ipynb`

Builds and validates two analytical tables:

- `data/processed/order_level_analytics.csv` — one row per `order_id`, with item/payment/review aggregates and delivery metrics.
- `data/processed/order_item_analytics.csv` — one row per `order_id + order_item_id`, enriched with product category, customer state, seller attributes and timestamps.

It also creates `outputs/analytical_model/model_validation_report.csv`, `payment_item_reconciliation_summary.csv`, `orders_without_item_records.csv`, and `analytics_data_dictionary.csv`.


### Notebook 03 — `notebooks/03_kpi_analysis_business_investigation.ipynb`

Uses the validated model to calculate executive KPIs and investigate late delivery and customer satisfaction by purchase month, customer state, category and seller exposure. It also summarizes fulfillment-stage durations, builds prioritization candidates, and writes concise business findings with explicit limitations.

Outputs are saved under `outputs/business_analysis/` and `outputs/charts/`. The notebook checks that Notebook 02's validation report contains only `PASS` results and runs additional quality checks before publishing KPI outputs.

## Setup in VS Code

1. Extract this project ZIP.
2. Place the original Olist dataset ZIP in `data/raw/` (the notebook reads it directly without extracting or modifying it).
3. Open the project folder in VS Code and create a Python environment:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

On macOS/Linux, activate with `source .venv/bin/activate`. Select the `.venv` interpreter as the Jupyter kernel.

**Run Notebook 01 first**, with `EXPORT_CLEANED_TABLES = True`. Review `outputs/data_quality/data_quality_gate.csv`. Then run Notebook 02 from top to bottom. The modeling notebook stops if required cleaned inputs are missing or if a structural validation check fails. Finally, run Notebook 03 to calculate the business KPIs and generate the segment-analysis tables and charts.

If data ZIP auto-discovery fails, set `DATA_ZIP_PATH` near the top of Notebook 01 or set the `OLIST_DATA_ZIP` environment variable.

## Modeling principles

- The original ZIP remains unchanged.
- Source tables have different grains. Items, payments and reviews are aggregated separately before joining to the order-level model.
- Left joins and `validate='many_to_one'` / `validate='one_to_one'` are used where appropriate; row counts and monetary totals are reconciled.
- Missing monetary totals remain null when no matching fact rows exist. A missing row is not automatically the same as a true zero amount.
- Delivery lateness is measured using **calendar dates**: actual delivery date later than estimated delivery date. This avoids falsely flagging a same-calendar-day delivery just because the actual timestamp is later than midnight on the estimated date.
- Stage duration metrics use elapsed timestamps and exclude intervals with missing or reversed event times.
- Payment-versus-item-plus-freight differences are diagnostic, not automatically errors and not profit.

## Initial business question

**Where are delivery delays concentrated, and how are late deliveries associated with customer reviews?**

The dataset is historical, mainly from 2016–2018. It does not contain a complete cost/profit model, marketing-spend data, or detailed carrier event logs. Analysis can identify historical patterns and prioritize investigations, but it cannot by itself prove causality or describe Olist's current operations.


## Dashboard stage (Notebook 04)

Run `notebooks/04_prepare_powerbi_dashboard.ipynb` after Notebooks 01–03. It creates `outputs/powerbi/` facts/dimensions and a validation report for Power BI. Follow `docs/powerbi_dashboard_guide.md`, `docs/powerbi_build_checklist.md`, and `sql/powerbi_measures.dax`. The repository provides dashboard-ready CSVs and DAX guidance; the `.pbix` report itself is created in Power BI Desktop.
