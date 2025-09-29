# Target State & Lab Mapping
| Concept (Target) | Lab Realisation |
|---|---|
| **ODP** — Raw/landing & SoR parity | `finance_odp.transactions` (synthetic) |
| **FDP** — Cross-domain standardised data | `finance_fdp.fx_rates` |
| **CDP** — Curated, business-ready views | `finance_cdp.v_transactions_harmonised`, `v_daily_totals*`, `v_monthly_totals` |
| **Controls** — Evidence, QA, policy | `finance_controls.dq_results`, `v_dq_latest`, `sp_run_dq_for_object` |
| **Catalog** — Discovery + badges | `finance_catalog.v_catalog` joining labels + latest DQ |

**Mapping notes**
- CDP harmonised view joins ODP with FDP rates to produce GBP amounts.
- Labels applied at resource creation (Terraform) become discoverable via `v_object_labels`.
- The **latest badge** is computed per object/run and joined into `v_catalog`.
