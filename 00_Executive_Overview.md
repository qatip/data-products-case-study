# Executive Overview
**Goal:** Demonstrate an end‑to‑end path for creating, governing, and assuring a banking **Data Product** across **ODP (Operational Data Platform)**, **FDP (Foundational Data Platform)**, and **CDP (Curated Data Platform)** using **BigQuery**, **Terraform**, and **Stored Procedures**.

## Value
- **Traceable pipeline** from raw (ODP) → foundational (FDP) → curated (CDP).
- **Automated DQ** with run-based results and a lightweight catalog badge.
- **Governed discovery** via labels and a consolidated `v_catalog` view.
- **Repeatable**: deterministic daily simulations (business dates) for demos or CI.

## What you see
- Synthetic SoR transactions ingested to ODP.
- FDP holds exchange rates (`fx_rates`).
- CDP offers a harmonised view (`v_transactions_harmonised`) and analytical rollups.
- A **single SP** (`sp_run_dq_for_object`) writes checks to `dq_results` and exposes a **latest badge** via `v_dq_latest` which feeds `finance_catalog.v_catalog`.
