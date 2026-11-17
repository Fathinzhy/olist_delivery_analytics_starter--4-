# Business KPI Definitions — Notebook 03

Use these definitions consistently in SQL, Python, Power BI, and the executive summary.

| KPI | Formula / rule | Population and caveat |
|---|---|---|
| Orders placed | Distinct `order_id` in the order model | All observed orders, regardless of final status |
| Canceled-order rate | Orders with status `canceled` / all orders | Does not explain why the order was canceled |
| Unavailable-order rate | Orders with status `unavailable` / all orders | Kept separate from cancellations |
| Eligible delivered orders | Status is `delivered`; purchase, actual-delivery, and estimated-delivery calendar dates exist; actual and estimate are not before purchase | Excludes delivered-status rows whose dates cannot support the KPI |
| Late delivered orders | Eligible delivered orders where actual delivery calendar date is later than estimated delivery calendar date | Same-calendar-day delivery is not late |
| Late delivery rate | Late delivered orders / eligible delivered orders | Conditional on delivery being recorded; not the rate across all orders placed |
| On-time / early delivery rate | Eligible delivered orders delivered on or before estimated calendar date / eligible delivered orders | Complements late delivery rate |
| Purchase-to-delivery lead time | Median actual delivery calendar date minus purchase calendar date | Different from delay against the estimate |
| Delay among late orders | Median actual delivery calendar date minus estimated delivery calendar date, restricted to positive values | Early delivery cannot offset late delay |
| Average order-level review score | First take mean of numeric review scores per order; then average across orders with a score | An order receives equal weight after its own reviews are averaged |
| Low-rated order | Order-level mean review score <= 2 | Chosen definition for this portfolio; disclose it in the dashboard |
| Low-rating share | Low-rated reviewed orders / orders with a numeric order-level score | Score-availability denominator is explicit |
| Delivered item-price total | Sum of item `price` among eligible delivered orders with item data | Excludes freight; not profit or accounting revenue |
| Average item price per eligible delivered order | Delivered item-price total / eligible delivered orders with item records | Excludes freight; name it explicitly rather than assuming it equals accounting AOV |
| Stage duration | End timestamp minus start timestamp in elapsed hours | Missing or negative intervals are excluded from that stage's distribution; report valid coverage |
| Segment excess late-order exposures | `max(0, observed late-order exposures - segment exposures * overall late rate)` | Descriptive prioritization aid; not estimated preventable orders or causal impact |

## Segment rules

- Customer state: minimum 500 eligible delivered orders for the main comparison table.
- Product category: minimum 100 eligible order-category exposures.
- Seller: minimum 100 distinct order-seller exposures.
- A multi-category or multi-seller order can appear in more than one segment. Segment exposure counts are not additive and do not attribute fault.
- Monthly trends use purchase month as the cohort. The output marks partial months, sharp low-volume tail, approximate follow-up coverage, and a minimum 500 eligible delivered-order denominator. Review those flags before displaying the trend.

## Interpretation rules

1. Do not infer causality from differences in review scores or delivery rates.
2. Do not interpret item price, freight, or payment totals as profit.
3. Keep unknown/missing categories and unmatched postal references visible rather than dropping them silently.
4. Distinguish historical descriptive metrics from current operational performance; Olist's dataset is primarily 2016–2018.
