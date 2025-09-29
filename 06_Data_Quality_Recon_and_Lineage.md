# Data Quality, Reconciliation & Lineage
## Checks Implemented
- **ODP (transactions)**  
  - `required_fields_period` — txnid/date/amount/currency non-null.
  - `amount_non_negative` — amount >= 0.
- **FDP (fx_rates)**  
  - `fx_freshness` — latest `rate_date` within tolerance of business date.
  - `fx_coverage_day` — required currency pairs present for each day.
- **CDP (v_transactions_harmonised)**  
  - `cdp_freshness_1d` — CDP data cut up-to-date.
  - `cdp_rowcount_matches_odp` — window parity with ODP.

## Evidence Model
- Rows written to `finance_controls.dq_results` (partitioned by `run_started_at`).
- Latest run per object via `finance_controls.v_dq_latest` (badge).

## Lineage (Conceptual)
ODP `transactions` → join with FDP `fx_rates` → CDP `v_transactions_harmonised` → analytical views.
