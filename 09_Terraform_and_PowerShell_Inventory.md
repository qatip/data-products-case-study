# Terraform & PowerShell Inventory
## Terraform
- `odp.tf` — ODP dataset + transactions table + labels.
- `fdp.tf` — FDP dataset + fx_rates table + labels.
- `cdp.tf` — CDP dataset + harmonised and analytical views (+labels).
- `catalog.tf` — controls datasets, `v_object_labels`, `v_dq_latest`, `v_catalog`.
- `stored_procedures.tf` — `sp_run_dq_for_object` (SQL procedure).
- `variables.tf`, `main.tf`, `gcs.tf`, `bq.tf` — wiring & providers.

## PowerShell
- `Step6-DataQualityChecks.ps1` — reads `terraform.tfvars` and calls:
  - ODP: `transactions` (TABLE)
  - FDP: `fx_rates` (TABLE)
  - CDP: `v_transactions_harmonised` (VIEW)
  - for the given `businessdate` (supports override).

### Tip
Keep **region** consistent (e.g., `europe-west1`) across datasets and SP references.
