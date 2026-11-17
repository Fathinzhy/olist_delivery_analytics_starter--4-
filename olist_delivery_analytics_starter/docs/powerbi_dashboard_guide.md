# Power BI Dashboard Guide

## Purpose
Build a two-page decision-support dashboard for the historical Olist dataset (2016–2018). The dashboard answers: **where are late deliveries concentrated, how are they associated with customer ratings, and which segments should be investigated first?** It is not a report of Olist's current performance.

## Data model
Run notebooks 01–04 in order. Import the seven CSVs in `outputs/powerbi/`:

- `fact_order_dashboard.csv` — one row per order. Use for overall KPI cards, purchase-month trend, customer state and delivery/review comparisons.
- `fact_order_category_dashboard.csv` — one row per order-category. Use for category-specific delivery and review comparisons.
- `fact_order_seller_dashboard.csv` — one row per order-seller. Use for seller-specific investigations.
- `dim_date.csv`, `dim_state.csv`, `dim_category.csv`, `dim_seller.csv` — slicer and chart dimensions.

### Relationships
Create one-to-many relationships with single-direction filtering from dimensions into facts:

| One side | Many side | Active? |
|---|---|---|
| `dim_date[date]` | each fact's `[purchase_date]` | Yes |
| `dim_state[customer_state]` | each fact's `[customer_state]` | Yes |
| `dim_category[product_category_label]` | `fact_order_category_dashboard[product_category_label]` | Yes |
| `dim_seller[seller_id]` | `fact_order_seller_dashboard[seller_id]` | Yes |

Do not relate fact tables directly. Mark `dim_date` as the date table using its `[date]` column. Sort `[year_month]` by `[year_month_sort]`. Disable Auto date/time if you prefer explicit, predictable date filtering.

## Page 1 — Executive Overview

**Audience:** operations manager / business lead. Canvas: 16:9. Keep the page to a few KPIs and high-signal visuals.

1. Header: “Delivery reliability & customer experience” and subtitle “Historical Olist marketplace orders, 2016–2018”.
2. Slicers: `dim_date[date]` (between/range), `dim_state[customer_state]`.
3. KPI cards: Eligible Delivered Orders; Late Delivery Rate; Late Orders; Mean Review Score; Low-Rating Share.
4. Line chart: `dim_date[year_month]` vs `Late Delivery Rate`; filter the visual to `dim_date[include_in_default_trend] = TRUE`. Tooltips: eligible orders, late orders and mean review score. This omits low-volume/partial source-tail months from the default trend. A user may include them deliberately for investigation.
5. Clustered column chart: average order-level review score by delivery outcome (late vs on-time/early). Show reviewed order count in tooltips.
6. Horizontal bar chart: late orders by customer state; optionally sort by excess late orders vs overall baseline. Add a minimum eligible-volume filter to avoid ranking tiny states.

**Narrative box:** “Late deliveries are associated with lower order-level review scores. Use the investigation page to identify where the observed exposure is concentrated.” Do not write that delays *cause* low ratings.

## Page 2 — Operational Investigation

**Audience:** operations team. Slicers: purchase date, customer state, category and seller where appropriate.

1. Category table/bar: category eligible order exposures, late exposures, late rate, gap vs overall baseline, mean review score, low-rating share and merchandise value. Default filter: minimum 100 eligible order-category exposures.
2. Seller table/bar: seller eligible order exposures, late rate, gap vs overall baseline, low-rating share and item price total. Default filter: minimum 100 eligible order-seller exposures; increase it if the chart becomes too noisy.
3. State comparison table: eligible delivered orders, late orders, late rate, rate gap vs baseline, excess late-order estimate, median delay among late orders and low-rating share. Default state minimum: 500 eligible orders, matching Notebook 03's comparison rule.
4. Fulfillment-stage visual: import `outputs/business_analysis/fulfillment_stage_summary.csv` as a separate descriptive table if desired. Plot median and 90th-percentile hours by stage, with valid-interval count/coverage in the tooltip. It is a historical aggregate and does not dynamically filter by state unless you generate stage-by-state data in a future iteration.

### How to prioritize a segment
Show both the **rate gap** and the **excess late exposure**. A high late rate in a small segment may affect fewer orders than a moderate rate in a large segment. For category/seller comparisons, use the selected date/state baseline; for state-to-overall comparisons, use the all-state baseline measure. Excess exposure is a comparison with a baseline, not a causal estimate or guaranteed number of preventable delays.

## Formatting and usability

- Use a plain light background, dark neutral text, one primary accent, and a restrained amber/red only for concerning performance.
- Title charts with the question or takeaway, not only the metric name.
- Use percentage formats for rates, one decimal for review scores, whole-number order counts, and a consistent currency format only for monetary measures.
- Include the population/denominator in a subtitle or tooltip for each rate.
- Use descriptive field names in the report; hide technical IDs where not useful.
- Add a visible note: “Historical public dataset; results describe 2016–2018 and do not indicate current Olist performance.”

## Known analytical caveats

- Delivery KPIs are conditional on delivered orders with valid actual/estimated dates and valid calendar-date chronology.
- Late means actual delivery **date** is later than estimated delivery **date**; same-day delivery is not late merely because the timestamp is later in the day.
- Low-rating status is based on the order-level mean review score being <= 2. This is a project-specific operational definition.
- Category and seller counts are exposures, not unique order totals across all segments; one order can involve multiple categories/sellers.
- GMV/item price is not profit. Full cost and margin data are not available.
- Historical associations do not prove causation.
