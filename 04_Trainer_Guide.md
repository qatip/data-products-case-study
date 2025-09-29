# Trainer Guide
## Narrative checkpoints
- **Why data products** (ownership, contracts, trust signals).
- **Layering** — ODP vs FDP vs CDP and clear interfaces.
- **Evidence** — run-based DQ; badges in catalog; labels for ownership.

## Common Pitfalls
- Variable escaping in PowerShell → use single-quoted templates for BigQuery backticks.
- BigQuery SP: **declare variables first**, avoid `DECLARE` inside IF blocks.
- Region mismatches — ensure dataset region alignment (europe-west1).

## Demo Flow Tips
- Start with `v_catalog` showing UNKNOWN, then run DQ → see badges flip.
- Use **businessdate** override to simulate multiple days.
