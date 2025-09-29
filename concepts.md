ODP — Operational Data Platform

What it is: The landing + operational layer that ingests source-of-record (SoR) data with minimal transformation.
Purpose: Preserve provenance, enable replay, and serve as the “single source of truth” for raw/operational facts.
Typical content: Raw/staged tables, lightly standardized fields (e.g., timestamps as DATE/TIMESTAMP, unified column names).
Ownership: Source domain teams (e.g., Payments/GL) with platform support.
In the lab: finance_odp.transactions (synthetic SoR transactions).
DQ focus: Schema/Nulls/Basic reasonableness per load window; run IDs tracked in finance_controls.dq_results.

FDP — Foundational Data Product

What it is: Reusable, trusted reference/lookup data that other products depend on.
Purpose: Provide stable, governed building blocks (dimensions, rates, codes) to avoid duplication and drift.
Typical content: Reference/master data (FX rates, charts of accounts, party/LEI, calendars).
Ownership: Usually a platform/domain “foundation” team; clear SLAs and refresh cadences.
In the lab: finance_fdp.fx_rates (daily FX to GBP).
DQ focus: Freshness vs. SLA, coverage/completeness of required keys/pairs per day.

CDP — Curated Data Product

What it is: Business-ready, consumable views/tables tailored to decisions, analytics, and reporting.
Purpose: Deliver harmonized, policy-compliant datasets with clear semantics and ownership.
Typical content: Conformed views, aggregates, and KPI layers (often joining ODP facts with FDP reference).
Ownership: Product teams in business domains (Finance in this case).
In the lab:

finance_cdp.v_transactions_harmonised (ODP transactions joined to FDP FX → GBP amounts)

Roll-ups like v_daily_totals, etc. (optional/secondary)
DQ focus: Curated freshness and reconciliation/consistency vs. ODP (e.g., row-count parity in the date window).

Cross-cutting (how they tie together)

Catalog: finance_catalog.v_catalog lists ODP/FDP/CDP objects + labels + latest DQ badge.

Labels: Owner/steward/product pulled via finance_controls.v_object_labels from BQ table/view labels.

DQ results: All checks write to finance_controls.dq_results; v_dq_latest shows the latest status per object.

SP entry point: finance_controls.sp_run_dq_for_object(dataset, name, obj_type, start_dt, end_dt, run_id) runs the relevant checks for ODP/FDP/CDP.
