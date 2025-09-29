# TL;DR — Banking Data Products Lab

**Why this exists**  
Banks need trustworthy, well-owned, timely data for regulatory reporting and analytics. Data **Products** make trust explicit by packaging data with ownership, SLAs, lineage and automated quality signals.

**What you’ll do in the lab**  
You’ll simulate a production-like flow from raw source to curated analytics, using Google BigQuery + Terraform:

- **ODP (Origin Data Product):** ingest synthetic SoR transactions (+ business labels).  
- **FDP (Federated Data Product):** standardise/enrich via FX rates (e.g., USD/EUR → GBP).  
- **CDP (Curated Data Product):** publish consumable views (e.g., daily/monthly totals, harmonised transactions).  
- **Catalog:** a lightweight cross-dataset view that shows metadata + **latest DQ badge** per object.

**Trust signals (Data Quality checks)**  
Each run writes to `finance_controls.dq_results`, and the latest status per object is exposed by `v_dq_latest` and surfaced in the catalog.

- **Required fields present** (completeness).  
- **Amounts non‑negative** (reasonableness).  
- **FDP freshness & coverage** for `fx_rates` (recent and complete for required currency pairs).  
- **CDP recon** (CDP vs ODP rowcount for a window) and freshness of curated views.

**How the lab maps to production**

| Production capability | Lab simulation |
|---|---|
| Business catalog (Collibra) | Table/view **labels** + `v_object_labels` |
| Technical catalog / lineage (Dataplex) | Terraform-managed datasets/views + clear naming; lineage out-of-scope but noted |
| Quality platform / observability | Stored proc `sp_run_dq_for_object` + `dq_results` + `v_dq_latest` |
| Publication / marketplace | `finance_catalog.v_catalog` cross-dataset view |
| Scheduling / orchestration | PowerShell + `bq query` over parameterised dates |

**How you run it (per business date)**  
1) Ingest → 2) Consolidate/transform → 3) Run SP `sp_run_dq_for_object` for ODP, FDP, CDP (PowerShell Step 6).  
Repeat for business dates like `2025‑08‑01`, `2025‑08‑02`, `2025‑09‑30`. The **catalog** reflects the latest DQ per object.

**What’s in scope**  
- ODP/FDP/CDP patterns, labels-as-metadata, DQ badges, and a lightweight catalog.

**What’s out of scope (for now)**  
- Interactive lineage UI, full Collibra integration, and BI dashboards (called out for future extension).

**Success looks like**  
- Catalog lists ODP/FDP/CDP objects with product/owner/steward labels.  
- `dq_results` contains runs for each date/object; `v_dq_latest` shows PASS/INFO/WARN/ERROR as expected.  
- CDP recon/freshness reflect your simulated pipeline state.

