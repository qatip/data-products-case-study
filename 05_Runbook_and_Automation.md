# Runbook & Automation
## Daily Control Flow (simulated)
1. Ingest ODP + FDP for **businessdate**.
2. Curate CDP harmonised view.
3. Run DQ for:
   - `finance_odp.transactions` (TABLE)
   - `finance_fdp.fx_rates` (TABLE)
   - `finance_cdp.v_transactions_harmonised` (VIEW)
4. Review `dq_results` and `v_dq_latest` → `v_catalog` badges.

## Step 6 PowerShell
- Reads `terraform.tfvars` for `project_id`, dataset names, `businessdate`.
- Calls `sp_run_dq_for_object` three times (ODP/FDP/CDP) for the business date.
- Quiet mode (`--format=none --quiet`) to keep console clean.
