# Power BI Build Checklist and Field Formatting

## Import order
1. Run notebooks 01–04 locally in sequence.
2. Get Data → Text/CSV, import all seven `outputs/powerbi/*.csv` files.
3. Set `dim_date[date]` and every fact `[purchase_date]` to Date (not Date/Time).
4. Set `fact_order_dashboard[order_id]`, all IDs and state/category labels to Text.
5. Set delivery flags and date-quality flags to True/False; `average_review_score` to Decimal number; monetary values to Decimal number; day/hour intervals to Decimal number.
6. Create model relationships per `powerbi_dashboard_guide.md`, mark `dim_date` as the date table, and sort YearMonth correctly.
7. Add measures from `sql/powerbi_measures.dax` and format them: rates as percentages; gaps labelled “pp” as numbers with one decimal; scores 0.00; counts whole numbers; currency according to your chosen BRL display format. Use the all-state baseline measures for state benchmarking and the contextual baseline for category/seller benchmarking.
8. Apply the default trend filter `dim_date[include_in_default_trend] = TRUE` on the monthly chart only, not as a page filter. Otherwise historical tail months disappear from every visual.
9. Test filter behavior with a single state and month, then reconcile the cards to Notebook 03 outputs.

## Recommended visual titles
- “How often are eligible delivered orders late?”
- “Does customer rating differ by delivery outcome?”
- “Where are late orders concentrated?”
- “Which categories have elevated late-delivery exposure?”
- “Which sellers warrant operational investigation?”
- “Which fulfillment stage has the longest tail?”

## Reconciliation before publishing
- Total orders: 99,441 in the dataset/model.
- Eligible delivered orders: 96,470 under the documented definition.
- Late orders: 6,534 using calendar-date comparison.
- Late delivery rate: 6.77% before report slicers.
- Mean order-level review score for reviewed late orders: approximately 2.27/5.
- Mean order-level review score for reviewed on-time/early orders: approximately 4.29/5.

If your imported measures differ, check filter interactions, date relationships, Boolean data types, and the population definition before changing the KPI formula.
