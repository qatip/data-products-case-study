# Data Products in Banking — Lab-Aligned

> **TL;DR (1–2 minute read)**
>
> **Goal.** Show how a bank can operate data as *products* with clear ownership, trust signals, and reuse across domains.
>
> **Lab mapping.** The lab implements a minimal but realistic three‑layer model:
>
> | Concept (Doc) | What the Lab Actually Does |
> |---|---|
> | **ODP — Operational Data Product** | `finance_odp.transactions` is loaded from a synthetic system‑of‑record and undergoes consolidation + basic DQ. |
> | **FDP — Foundational Data Product** | `finance_fdp.fx_rates` provides reference/standardisation (partitioned on `rate_date`). |
> | **CDP — Curated Data Product** | `finance_cdp.v_transactions_harmonised` joins ODP with FDP rates to produce GBP‑normalised transactions; additional curated rollups live in CDP. |
> | **Ownership / Metadata** | Business ownership surfaced via BigQuery **labels** exposed through `finance_controls.v_object_labels`. |
> | **Trust / DQ badge** | All data quality checks write to `finance_controls.dq_results`; latest badge per object is exposed in `finance_controls.v_dq_latest`. |
> | **Queryable catalog** | `finance_catalog.v_catalog` lists ODP/FDP tables and selected CDP views with owner/steward + latest DQ status. |
> | **Operational cadence** | “Business day” is simulated (e.g., 2025‑08‑01). Step‑6 PowerShell triggers DQ across ODP, FDP and CDP for that date. |
>
> **Scope (simplifications):**
> - One domain (Finance) and a small object set; representative checks (completeness, non‑negatives, freshness/coverage, rowcount recon).  
> - Labels/ownership and a visible DQ badge are implemented; fine‑grained security, PII handling, alerts and automated incident flows are out of scope.  
> - Lineage is expressed through SQL/view dependencies; UI lineage (e.g., Dataplex) can be wired later.
>
> **Key objects (searchable in BigQuery):**
>
> - **ODP**: `finance_odp.transactions`  
> - **FDP**: `finance_fdp.fx_rates`  
> - **CDP**: `finance_cdp.v_transactions_harmonised` (+ curated views)  
> - **Controls**: `finance_controls.dq_results`, `finance_controls.v_dq_latest`, `finance_controls.v_object_labels`, `finance_controls.sp_run_dq_for_object`  
> - **Catalog**: `finance_catalog.v_catalog`
>
> **Takeaway.** The lab proves the operating model: product ownership + quality telemetry + catalog visibility. Teams can scale by adding new products, views, and checks, not by changing the principles.

---

## 1. Why Data Products (Banking Context)

Banks grapple with siloed datasets, unclear ownership, and fragile cross‑team dependencies. “Projects ship, but products run”: moving to **data products** means each dataset (or view) is owned, documented, governed, and observable like an application. This enables:
- **Trust:** Consumers can see health and provenance (DQ badge, owner/steward, labels).
- **Re‑use:** Foundational products (e.g., FX rates) are shared across curated analytics.
- **Change velocity:** Clear interfaces reduce cross‑team coupling.
- **Compliance:** Ownership and lineage make controls auditable.

## 2. Target Operating Model

### 2.1 Layers
- **ODP (Operational)** — Domain‑owned raw/cleansed data close to source, minimal shaping, daily cadence.  
- **FDP (Foundational)** — Cross‑domain reference/standardisation (e.g., FX rates), keyed and high‑quality, reused by many.  
- **CDP (Curated)** — Consumer‑shaped views / marts / analytics built from ODP + FDP with business logic applied.

### 2.2 Ownership & Metadata
- Product metadata is stored as **BigQuery labels** on tables/views (e.g., `product`, `owner`, `steward`, `domain`).  
- `finance_controls.v_object_labels` reads labels from INFORMATION_SCHEMA and normalises them for joins.

### 2.3 Trust & Observability
- All checks write run‑scoped results into `finance_controls.dq_results` (partitioned on `run_started_at`).  
- `finance_controls.v_dq_latest` computes a **badge** (PASS/INFO/WARN/ERROR) per object’s latest run.  
- `finance_catalog.v_catalog` joins object inventory + labels + latest badge for one place to browse product health.

## 3. Lab Architecture (Concrete)

```
+----------------------+      +---------------------+      +--------------------+
|  ODP (finance_odp)   |      | FDP (finance_fdp)   |      |  CDP (finance_cdp) |
|  transactions        | ---> | fx_rates            | ---> | v_transactions_... |
|  (synthetic SOR)     |      | (partitioned)       |      | + curated rollups   |
+----------------------+      +---------------------+      +--------------------+
             \                           |                          |
              \                          |                          |
               \-------------------------+--------------------------+
                                   Controls & Catalog
             dq_results, v_dq_latest, v_object_labels, sp_run_dq_for_object, v_catalog
```

**Terraform** provisions datasets, tables, and views. **Stored procedure** `sp_run_dq_for_object` runs checks. **PowerShell Step‑6** triggers runs for a target business date.

## 4. Data Quality Model (Implemented in Lab)

### 4.1 Controls schema
- `finance_controls.dq_results` (time‑partitioned): one row per check per run (`run_id`, `dataset`, `name`, `obj_type`, `check_id`, `dimension`, `severity`, `status`, `observed_value`, `expected_rule`, `message`, `owner`, `steward`).  
- `finance_controls.v_dq_latest`: selects the latest run per object and computes a roll‑up badge (ERROR > WARN > INFO > PASS).

### 4.2 Checks shipped with the lab
- **ODP (transactions)**:  
  - *Completeness* — non‑null `txnid`, `date`, `amount`, `currency` in the business window.  
  - *Reasonableness* — `amount >= 0` (illustrative, can be domain‑tuned).
- **FDP (fx_rates)**:  
  - *Freshness* — latest `rate_date` within tolerance of `p_end_dt`.  
  - *Coverage* — required currency pairs present each day in window.
- **CDP (v_transactions_harmonised)**:  
  - *Freshness* — `MAX(date)` meets the window.  
  - *Consistency* — rowcount matches ODP over the same window.

All checks record **owner/steward** derived from labels so operational routing is obvious.

## 5. Catalog & Labels

- `finance_catalog.v_catalog` builds a cross‑dataset inventory from INFORMATION_SCHEMA:  
  - ODP: tables; FDP: tables; CDP: **selected** views (start with `v_transactions_harmonised`).  
  - Joins `v_object_labels` for `product`, `owner`, `steward`.  
  - Joins `v_dq_latest` to show the latest **dq_status** per object.
- Labels are populated from CSVs during Terraform apply and written to BigQuery resources as labels.

## 6. Operational Cadence (Simulated “Business Date”)

The lab uses a **business date** (e.g., `2025-08-01`) to simulate daily operations. You can replay multiple days by changing `businessdate` or invoking Step‑6 with an override.

- **Stored procedure:**  
  `CALL \`<project>.<controls_dataset>.sp_run_dq_for_object\`(p_dataset, p_name, p_obj_type, p_start_dt, p_end_dt, p_run_id)`
- **PowerShell Step‑6** runs the procedure three times per day:  
  - ODP: `transactions` (TABLE)  
  - FDP: `fx_rates` (TABLE)  
  - CDP: `v_transactions_harmonised` (VIEW)

## 7. How to Extend (Roadmap)

- **Add products**: introduce more ODP/FDP datasets and attach labels. The catalog/view logic already scales.  
- **Add checks**: schema drift, referential integrity, distribution/thresholds, SLA breach alerts (Cloud Scheduler + Pub/Sub).  
- **Lineage UI**: publish to Dataplex/Data Catalog for end‑to‑end visual lineage.  
- **Security/PII**: column‑level classifications, row‑level security, policy tags.  
- **Incident mgmt**: route FAIL/WARN via Chat/Email on duty rota using owner/steward labels.

## 8. Roles & RACI (Lightweight)

- **Product Owner** — accountabilities, roadmap, SLA, approval of breaking changes.  
- **Steward** — data quality, definitions, glossaries, runbook.  
- **Engineers** — pipelines, infra (Terraform), tests, deployments.  
- **Consumers** — read CDP, raise issues via the catalog entry.

## 9. Glossary (Selected)

- **Data Product** — a dataset (or view) owned like a product: documented, discoverable, high‑quality, with SLOs.  
- **ODP/FDP/CDP** — operational, foundational, curated layers.  
- **DQ Badge** — PASS/INFO/WARN/ERROR roll‑up for the object’s **latest run**.  
- **Business Date** — simulated as‑of date controlling windowed checks.

## 10. Appendix — Key References (Lab Objects)

- DQ: `finance_controls.dq_results`, `finance_controls.v_dq_latest`, `finance_controls.sp_run_dq_for_object`  
- Metadata: `finance_controls.v_object_labels`  
- Catalog: `finance_catalog.v_catalog`  
- ODP: `finance_odp.transactions`  
- FDP: `finance_fdp.fx_rates`  
- CDP: `finance_cdp.v_transactions_harmonised`
