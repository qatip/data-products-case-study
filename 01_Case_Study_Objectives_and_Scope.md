# Case Study — Objectives & Scope

## Objectives
1. Prove a **metadata-driven** approach to deploy/manage data products on BigQuery.
2. Demonstrate the **trust backbone**: reconciliation, data quality, lineage.
3. Show how **labels/metadata** feed a queryable catalog view with DQ badges.
4. Provide a **repeatable lab** that mimics enterprise patterns.

## In scope
- BigQuery datasets: `finance_odp`, `finance_fdp`, `finance_cdp`, `finance_controls`.
- Sample data: ODP transactions; FDP FX rates.
- Curated views (CDP): harmonised transactions + daily/monthly/by-currency aggregates.
- DQ + reconciliation stored procedure, `dq_results` table, `v_dq_latest` badge view.
- Terraform for infra/objects; PowerShell for orchestration.

## Out of scope (for now)
- Collibra/Edge native integrations (simulated via CSV → labels).
- Full DQ rule catalogue (we implement a focused, extensible set).
- Production dashboards (we surface SQL/views you can wire to BI later).
