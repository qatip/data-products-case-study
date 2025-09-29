# Case Study Objectives & Scope
## Objectives
1. Build a **banking data product** with clear separation of **ODP/FDP/CDP**.
2. Enforce **basic DQ** (completeness, reasonableness, freshness/coverage).
3. Provide a **catalog view** enriched with **labels** and **latest DQ** badge.
4. Support **daily simulation** by **business date** to mirror production cadence.
5. Capture **run-based evidence** in `dq_results` for traceability.

## Scope
- **In-scope**
  - BigQuery datasets: `finance_odp`, `finance_fdp`, `finance_cdp`, `finance_controls`, `finance_catalog`.
  - DQ: `required_fields_period`, `amount_non_negative`, `fx_freshness`, `fx_coverage_day`, `cdp_freshness_1d`, `cdp_rowcount_matches_odp`.
  - Labels via `INFORMATION_SCHEMA.TABLE_OPTIONS` → view `v_object_labels`.
  - Latest DQ badge via `v_dq_latest`; catalog join via `v_catalog`.

- **Out-of-scope**
  - Real-time streaming, CDC, masking/tokenisation, cost governance.
  - External tooling (e.g., Collibra) — simulated via CSV-driven labels.
