# Governance & Marketplace
## Labels
- Applied via Terraform (`labels {}` blocks) or CSV-driven locals.
- Exposed via `finance_controls.v_object_labels` (INFORMATION_SCHEMA.TABLE_OPTIONS).

## Catalog
- `finance_catalog.v_catalog` lists ODP/FDP tables and selected CDP views.
- Joins labels + latest DQ to surface **owner/steward/product** and **DQ badge**.

## Extending
- Add tags like `pii`, `retention`, `sla_tier` as labels and surface them in `v_catalog`.
