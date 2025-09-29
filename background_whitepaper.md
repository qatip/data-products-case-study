# Banking Data Products: Background Whitepaper (ODP · FDP · CDP)

## TL;DR
Banks can speed up delivery, improve trust, and safely reuse data by organizing around **three layers**:
- **Operational Data Platform (ODP):** clean, governed copies of source data optimized for ingestion and lineage.
- **Foundational Data Products (FDP):** curated, reusable datasets (e.g., FX rates) with SLAs, quality contracts, and owners.
- **Consumer Data Products (CDP):** domain-facing views, marts, or APIs built from FDP + ODP to answer real business questions.

Treating datasets as **products** (not projects) means each asset has: a product owner, clear interface/contract, documented purpose, and measurable quality. Discovery happens through a **catalog/marketplace** with business metadata and “badges” (freshness, coverage, tests).

---

## Why “Data Products” for Banks
- **Time-to-value:** standard interfaces and reusable products reduce bespoke, one-off pipelines.
- **Trust & compliance:** products ship with tests, lineage, SLAs, RACI, and audit-ready metadata.
- **Risk reduction:** controlled promotion from ODP → FDP → CDP ensures transformations are transparent and reversible.
- **Scalability:** decentralize ownership to domains while centralizing policy controls and platform guardrails.
- **Monetization & reuse:** make well-governed datasets discoverable across use cases (pricing, finance, risk, fraud, marketing).

---

## The Three-Layer Pattern

### 1) Operational Data Platform (ODP)
**Purpose:** land, standardize, and preserve high-fidelity source data with audit trails and lineage.
- **Typical sources:** core banking, cards, CRM, ledgers, payments.
- **Design traits:** schema-on-write, slowly-evolving contracts, immutable+append strategies, CDC capture, partitioning by event/business date.
- **Governance:** baseline PII handling, row-level/column-level policies, source-to-ODP lineage.

### 2) Foundational Data Products (FDP)
**Purpose:** curated, reusable building blocks (e.g., **FX rates**, party/identity golden record, reference data).
- **Design traits:** opinionated harmonization; versioned contracts; documented dimensions/measures; explicit SLAs.
- **Quality:** freshness, completeness, coverage checks; owner & steward defined; change logs.
- **Interfaces:** queryable tables/views, governed exports, and product-aligned APIs.

### 3) Consumer Data Products (CDP)
**Purpose:** domain-oriented, outcome-driven products built from FDP (+ sometimes ODP).
- **Examples:** daily totals in GBP, risk exposure views, marketing propensity features.
- **Design traits:** business terms in the schema; fit-for-purpose transformations; semantic alignment with the consuming domain.
- **Quality:** scenario-specific checks (recon to ODP/FDP, timeliness, reasonableness).

---

## Roles & Operating Model
- **Product Owner (per product):** purpose & scope, roadmap, acceptance criteria, business SLAs.
- **Steward/Data SME:** definitions, data quality rules, glossary, policy alignment.
- **Engineering (platform & domain):** pipelines, contracts, tests, CI/CD, security controls.
- **Risk/Compliance:** policy-as-code guardrails; data classification and retention; model risk alignment for AI use.
- **Consumers:** subscribe to products, provide feedback, raise requests via product backlogs.

RACI is explicit for each product. Promotions (ODP→FDP→CDP) follow change control with automated test gates.

---

## Product Lifecycle (Bank Context)
1. **Inception:** define the business problem, data scope, KPIs, and risks.
2. **Minimum Viable Product:** data contract, owner/steward named, initial tests & lineage, basic catalog entry.
3. **Hardening:** add completeness/freshness/coverage checks; SLOs/SLAs; load/performance tuning; access policies.
4. **Scale:** publish to catalog/marketplace; version & deprecate responsibly; enable observability & incident runbooks.
5. **Evolve/Retire:** manage schema evolution with semantic versioning; communicate deprecation windows.

---

## Architecture Overview (Conceptual)
- **Storage/Compute:** cloud warehouse/lakehouse with table & view layer (partitioning + clustering).
- **Metadata & Catalog:** business metadata (owner, steward, purpose), technical lineage, and quality badges.
- **Quality & Observability:** run-based DQ results per product; last-known status surfaced in the catalog.
- **Security & Policy:** data classification, fine-grained access controls, masking, tokenization where needed.
- **CI/CD:** infrastructure as code for datasets, views, routines; test gates at pull request and promotion time.

**Typical flow:** Sources → ODP (raw+standardized) → FDP (curated + contracts) → CDP (domain solutions).

---

## Quality Controls (Essentials)
- **Freshness:** max event date within tolerated lag.
- **Completeness:** required fields present; expected day-level coverage.
- **Consistency & Recon:** CDP counts reconcile with ODP/FDP windows; derived measures are explainable.
- **Reasonableness:** domain-specific thresholds (e.g., non-negative amounts, bounded FX changes).
- **Badging:** aggregate latest outcomes into a simple PASS/WARN/ERROR label per product.

Run-level results are stored with: run_id, timestamps, object key, check_id, severity, status, and steward/owner attribution.

---

## Marketplace & Monetization
- **Discovery:** search by business terms, tags, and domains; preview schemas and example queries.
- **Contract-first access:** consumers agree to SLAs and usage constraints; request access via workflows.
- **Chargeback/Showback:** optional, to encourage responsible consumption and cost accountability.
- **Feedback & NPS:** treat products like software—collect ratings, issues, and enhancement requests.

---

## Interoperability with AI/ML
- **Feature Reuse:** FDPs become feature sources; contracts stabilize training/serving parity.
- **Governance:** link products to model cards, lineage, and approvals; evidence for Model Risk Management.
- **Data Minimization:** provide least-privilege, purpose-bound slices for downstream modeling.

---

## Risks & Controls (Banking)
- **PII & Confidentiality:** enforce masking, tokenization, and purpose-based access.
- **Fairness & Bias:** ensure reference data and aggregations don’t embed discriminatory proxies.
- **Financial Controls:** SOX/GL compliance; reconciliations; audit trails on transformations and access.
- **Resilience:** backup, DR strategies, incident response, and error budgets for data SLAs.

---

## Glossary
- **Data Product:** a discoverable, documented, supported data asset with an owner, contract, and SLOs.
- **Contract:** schema + semantics + quality + SLA expectations consumers rely on.
- **Lineage:** traceable path from sources through transformations to consumers.
- **Badge:** simplified label (PASS/WARN/ERROR/UNKNOWN) reflecting latest test outcomes for a product.
- **Promotion:** controlled movement from ODP→FDP→CDP with automated gates.

---

## Closing
Organizing the estate into **ODP → FDP → CDP** with a **product mindset** lets banks deliver faster, reduce risk, and scale responsibly. The winning combination is **contract-first design**, **automated quality**, and a **catalog-driven marketplace** that turns data into governed, reusable products.
