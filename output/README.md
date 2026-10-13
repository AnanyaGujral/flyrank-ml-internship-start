# `output/` — capstone artifacts (paper-facing)

These files are generated **reproducibly** by `work/notebooks/capstone.ipynb`. Run that notebook
top to bottom (it needs no dataset token) to regenerate every file here.

| file | what it is |
|---|---|
| `capstone_results.json` | machine-readable summary of both scales (bundled slice + full release) |
| `evaluation_summary.md` | model-vs-baseline tables on the **same** held-out clients, plus the random-vs-grouped leakage contrast |
| `data_quality_summary.md` | pool size, base rates, exclusions, privacy note |
| `ranked_recommendations.md` | the action playbook + review-first reason-code counts |
| `charts/precision_at_k.svg` | ranking quality vs the base rate (full release) |
| `charts/split_comparison.svg` | how client leakage inflates a naive random-split score |
| `charts/feature_importance.svg` | what the random forest leans on |
| `charts/action_mix.svg` | share of the pool in each of the four actions |

**Two scales, both honest:**

- **Full release** (the paper's headline) — 100,893 pages × 43 clients, March 2026 features →
  April 2026 decline label. The per-client numbers were computed by
  `w03_data_contract.ipynb` … `w07_action_playbook.ipynb` on `FlyRank/internship-warehouse`
  (public-but-gated) and their receipts are committed in `work/outputs/*.json`. Regenerating them
  requires an `HF_TOKEN` with read access.
- **Bundled slice** (offline reproduction) — `data/raw/content_refresh_anonymized.csv`, 30,000 pages,
  same-window proxy label. Recomputed live by the capstone notebook.

`portfolio/assets/*.svg` are the published copies of the four charts (same run, same files); the
paper bundle in `portfolio/` is self-contained so it can be served by GitHub Pages.

No CSV exports are committed here on purpose: the repository's data-leak CI blocks committed CSVs
and the values you'd want are already in the tables above. No client names, domains, URLs, or
queries appear anywhere — only hashed ids and aggregates.
