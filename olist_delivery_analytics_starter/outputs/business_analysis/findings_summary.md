# Business Findings — Delivery Reliability & Customer Experience

## Baseline
- Orders in the order-level model: **99,441**.
- Eligible delivered orders: **96,470**.
- Late delivered orders: **6,534 (6.77%)**.
- Mean order-level review score for late orders with scores: **2.27/5**.
- Mean order-level review score for on-time/early orders with scores: **4.29/5**.
- Low-rating share (order-level mean score <=2): **62.36%** among reviewed late orders versus **9.23%** among reviewed on-time/early orders.

## Investigation candidates
- Highest late rate among states with at least 500 eligible orders: **MA (17.43%, n=717)**.
- Largest positive excess late-order count vs the overall baseline among qualified states: **RJ (659 excess orders, n=12,350)**.
- Highest late rate among categories with at least 100 eligible order exposures: **audio (11.78%, n=348)**.
- Monthly late delivery rates vary materially across eligible purchase cohorts: **2018-03 (18.96%, 1,328/7,003)** versus **2018-06 (1.16%, 71/6,096)**. Validate the trend and investigate order mix and fulfillment-stage differences before attributing a cause.

## Proposed next action
1. Investigate the states with the largest number of late orders above the overall baseline, not only the highest rate.
2. For those regions, break down valid fulfillment-stage durations and compare product mix and seller exposure.
3. Track late delivery rate and low-rating share after a defined operational intervention; do not treat this historical analysis as proof of causality.

## Limitations
- This is historical marketplace data, mainly from 2016–2018; it is not current operational performance.
- Delivery KPIs are conditional on delivered orders with valid delivery dates. Orders still in progress or not delivered are not in that denominator.
- The late-order/review association is unadjusted; order mix, geography, category, seller and other factors may differ.
- State, category and seller results are exposure-based. One order can involve multiple categories or sellers, so segment counts are not additive.
- The dataset lacks complete logistics event traces, carrier assignment, operating cost and profit measures.