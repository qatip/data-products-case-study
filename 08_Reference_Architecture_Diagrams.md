# Reference Architecture (Text)
```
[ODP: finance_odp]
  └─ transactions (TABLE)
       │
       ├─ DQ (SP) → dq_results → v_dq_latest → v_catalog
       │
[FDP: finance_fdp]
  └─ fx_rates (TABLE)
       │
       ├─ DQ (freshness/coverage) → dq_results
       │
[CDP: finance_cdp]
  └─ v_transactions_harmonised (VIEW of ODP×FDP)
       ├─ v_daily_totals*
       └─ v_monthly_totals

[Controls: finance_controls] → dq_results, v_dq_latest, v_object_labels
[Catalog : finance_catalog]  → v_catalog (labels + DQ badge)
```
