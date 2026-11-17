# Analytical Data Model — Design and Validation

## Model grains

| Model | Grain | Primary key | Intended use |
|---|---|---|---|
| `order_level_analytics.csv` | One row per order | `order_id` | Order-level delivery KPIs, customer review comparisons, monthly trends, customer-state cuts |
| `order_item_analytics.csv` | One row per order line | `order_id + order_item_id` | Product category, seller/state, item price and freight analyses |

## Why there are two models

An order may include multiple item lines, several payment records and multiple reviews. Joining all raw detail tables before aggregation can multiply rows (join fan-out) and inflate money totals. Notebook 02 first aggregates item lines, payments and reviews independently to order grain, then joins those aggregates to the one-row-per-order order table. The item-grain table remains separate for category/seller questions.

## Key derived metrics

| Field | Definition |
|---|---|
| `item_price_total` | Sum of item `price` by order. |
| `freight_value_total` | Sum of item `freight_value` by order. |
| `item_total_including_freight` | Sum of item price plus freight; not profit. |
| `payment_value_total` | Sum of payment `payment_value` by order; not profit or necessarily net revenue. |
| `average_review_score` | Mean of available review scores linked to the order. |
| `is_low_rated_order` | True when mean order review score is at most 2; null if no score exists. |
| `purchase_to_approval_hours` | Elapsed hours purchase → approval, only when timestamps exist and are chronological. |
| `approval_to_carrier_hours` | Elapsed hours approval → carrier handoff, only when timestamps exist and are chronological. |
| `carrier_to_delivery_hours` | Elapsed hours carrier handoff → customer delivery, only when timestamps exist and are chronological. |
| `delivery_delay_days` | Actual delivery **calendar date** minus estimated delivery **calendar date**. Positive means late, zero means same-day, negative means early. |
| `delivery_metric_eligible` | Delivered status, purchase/actual/estimated dates present, and actual/estimated dates not before purchase date. This field defines the denominator for the late-delivery rate. |
| `is_late_delivery` | True for eligible orders with `delivery_delay_days > 0`; null for ineligible orders. |
| `payment_minus_items_and_freight` | Payment total minus item price and freight total; reconciliation diagnostic only. |

## Validation results on the supplied dataset

Notebook 02 was run against the supplied Olist CSVs. All 10 structural checks passed:

- 99,441 orders retained and unique in the order-level model.
- 112,650 item lines retained with unique order-item keys.
- Customer and seller geolocation joins preserved their base row counts.
- Item-price total reconciled to 13,591,643.70.
- Freight total reconciled to 2,251,909.54.
- Payment total reconciled to 16,008,872.12.
- All 99,224 source review rows were represented in the review-score aggregation.

## Financial reconciliation is diagnostic

There were 98,665 orders with both payment and item-plus-freight totals. Most matched closely; 378 differed by more than 0.01 in the supplied data. There were also 775 orders with payment records but no item rows, totaling 162,591.95 in payment value, and one order with item rows but no payment records. These should be reviewed in order-status context, especially for cancellation/unavailability. Do not force these differences to zero or describe them as profit.

## Delivery KPI definition correction

The estimated-delivery column often represents a date at midnight. Comparing the full actual-delivery timestamp to that midnight timestamp overstates lateness for orders delivered later on the same calendar day. The accepted definition therefore compares calendar dates. Under this definition in the supplied historical data:

- Eligible delivered orders: 96,470
- Late orders: 6,534
- Late-delivery rate: 6.77%
- Mean review score among late orders with scores: 2.27 / 5 (6,381 scored orders)
- Mean review score among on-time/early orders with scores: 4.29 / 5 (89,443 scored orders)
- Low-rating share (order mean score ≤ 2): 62.36% for late orders with scores versus 9.23% for on-time/early orders with scores

These are descriptive historical results. They show an association, not proof that late delivery causes low reviews. Segment comparisons and business recommendations should account for order mix, sample size and other possible explanations.
