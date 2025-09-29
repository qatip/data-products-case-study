# Hands-On Lab Guide (Student)
## Prereqs
- gcloud / bq CLI authenticated; project set.
- Terraform installed; BigQuery APIs enabled.

## High-level Steps
1. **Provision** datasets & tables: ODP, FDP, CDP, Controls, Catalog (Terraform).
2. **Ingest**: load synthetic ODP transactions and FDP `fx_rates` (sampledata).
3. **Curate**: create CDP `v_transactions_harmonised` and rollups.
4. **Run DQ**: invoke SP for ODP, FDP, and CDP (Step 6 PowerShell script).
5. **Verify**: query `finance_controls.dq_results`, `v_dq_latest`, and `finance_catalog.v_catalog`.
6. **Repeat** for business dates (e.g., `2025-08-01`, `2025-08-02`, `2025-09-30`).

## Key Queries
- Labels: `SELECT * FROM \`<project>.finance_controls.v_object_labels\` LIMIT 10;`
- Latest DQ: `SELECT * FROM \`<project>.finance_controls.v_dq_latest\` LIMIT 10;`
- Catalog: `SELECT * FROM \`<project>.finance_catalog.v_catalog\` ORDER BY dataset, name LIMIT 20;`
