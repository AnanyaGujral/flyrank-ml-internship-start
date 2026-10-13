# PROGRESS — FlyRank ML Internship Capstone

_Last updated: 2026-10-14_

**Owner:** Ananya Gujral · **Repo:** https://github.com/AnanyaGujral/flyrank-ml-internship-start
**Track:** Machine Learning · **Lane:** 2 — Refresh / Content Opportunity Scoring

---

## 1. Audit summary (what was already here)

| Area | State on arrival | State now |
|---|---|---|
| `work/notebooks/w01_research_question.ipynb` (ML-02) | ✅ filled + outputs | unchanged |
| `work/notebooks/w02_ml_task_framing.ipynb` (ML-03) | ✅ filled + outputs | unchanged |
| `work/notebooks/w03_data_contract.ipynb` (ML-04) | ✅ filled + outputs (warehouse) | unchanged |
| `work/notebooks/w03_feature_leakage_check.ipynb` (ML-05) | ⬜ empty skeleton | ✅ **filled + executed offline** |
| `work/notebooks/w04_signal_audit.ipynb` (ML-06) | ⬜ empty skeleton | ✅ **filled + executed offline** |
| `work/notebooks/w04_baseline_score.ipynb` (ML-07) | ✅ filled + outputs (warehouse) | unchanged |
| `work/notebooks/w05_model.ipynb` (ML-08) | ✅ filled + outputs (warehouse) | unchanged |
| `work/notebooks/w06_validation_audit.ipynb` (ML-09) | ✅ filled + outputs (warehouse) | unchanged |
| `work/notebooks/w07_action_playbook.ipynb` (ML-10) | ✅ filled + outputs (warehouse) | unchanged |
| `work/notebooks/capstone.ipynb` (ML-11/12) | ⬜ empty skeleton | ✅ **filled + executed offline** |
| `outputs/` (starter reference) | ✅ committed examples | unchanged (reverted after a validation run) |
| `output/` | ⬜ missing — required | ✅ **created (charts, tables, recommendations)** |
| `portfolio/` | ⬜ missing — required | ✅ **created (the research paper)** |
| `submission/paper_url.txt` | ⬜ placeholder | ⬜ still placeholder — **deploy blocked** |
| Root `README.md` | ⬜ starter text | ✅ capstone section + structure + reproduction |

Reference repo inspected (read-only clone): https://github.com/ROHIT-25607/FLYRANK-INTERN. It deploys
its paper as `docs/index.html` and keeps receipts in `work/outputs/*.json`. We adopted its *ideas*
(receipts, an executable paper-mirror notebook) but **not** its files, numbers, charts, or structure —
our paper lives in `portfolio/` (as required) and all results are ours.

## 2. Chosen lane and research question

- **Lane 2 — Refresh / Content Opportunity Scoring.**
- **Question:** *Among a client portfolio's already-visible pages, can trailing search and engagement
  signals rank the pages that are about to decline well enough to beat a transparent rule — measured at
  the top of the queue, under a client-grouped (new-client) validation design?*

## 3. Data & access

- **Bundled, offline, reproducible:** `data/raw/content_refresh_anonymized.csv` — 30,000 pages × 44 cols.
- **Full release:** `FlyRank/internship-warehouse` (Hugging Face, gated) — **not reachable here**
  (`HTTP 401`, no `HF_TOKEN` in this environment). The warehouse study was executed by the intern and its
  receipts are committed inside the w03–w07 notebook outputs and transcribed into `work/outputs/*.json`.
  Re-running `w03`–`w07` requires a read token.

## 4. Task checklist

- [x] Local repo + git audit (clean tree, branch `main`)
- [x] Reference repo inspection (tree, paper, deployment convention)
- [x] Chosen lane + research question documented
- [x] `PROGRESS.md` created
- [x] Fill + execute `work/notebooks/w04_signal_audit.ipynb` (offline, bundled slice)
- [x] Fill + execute `work/notebooks/w03_feature_leakage_check.ipynb` (offline, bundled slice)
- [x] Write + execute `work/notebooks/capstone.ipynb` (offline run + regenerates `output/` from receipts)
- [x] Commit capstone receipts `work/outputs/*.json`
- [x] Generate `/output` artifacts (4 charts, 3 tables, `capstone_results.json`, `README.md`)
- [x] Build `/portfolio` research paper (9 sections, 5-sentence abstract, flyrank.ai credit)
- [x] Update root `README.md` + reproduction instructions
- [x] Privacy / publication review (see §6)
- [x] Add GitHub Pages deployment config (`.github/workflows/deploy-pages.yml`)
- [x] Push to `origin/main` (3 commits) — done by the account owner
- [x] Deploy + verify live URL → `submission/paper_url.txt` written

## 5b. Deployment (verified)

- **Live paper:** https://ananyagujral.github.io/flyrank-ml-internship-start/ — HTTP 200.
- Served from the `portfolio/` folder via `.github/workflows/deploy-pages.yml`
  (Pages source = GitHub Actions).
- Verified live: title, abstract, all 9 section anchors, the exact
  "Built on the FlyRank ML Internship dataset" credit linking to https://flyrank.ai, the repo link,
  and all four chart assets (`assets/*.svg`) return HTTP 200.
- `submission/paper_url.txt` contains exactly that one URL (59 bytes, no trailing newline).

## 5. Validation performed

| Check | Result |
|---|---|
| `python scripts/run_all.py` (bundled slice, starter pipeline) | runs end-to-end; RF P@50 0.740 vs rule 0.240, base 0.542 (client-holdout) |
| `work/notebooks/w04_signal_audit.ipynb` executed top-to-bottom | 0 errors; receipt written |
| `work/notebooks/w03_feature_leakage_check.ipynb` executed top-to-bottom | 0 errors; leak probe honest AUC 0.62 → leaky 0.999/1.00 |
| `work/notebooks/capstone.ipynb` executed top-to-bottom | 0 errors; regenerated all `output/` + `portfolio/assets/` |
| Notebook CI condition (`code>200 chars ⇒ has outputs`) | ✅ all 10 notebooks have outputs, 0 error cells |
| Ambient starter outputs (`outputs/`) | reverted after the run so the user's committed reference stays intact |
| `portfolio/index.html` HTML structure | well-formed, balanced tags; all 4 asset paths resolve |
| `portfolio/assets/*.svg` | all 4 parse as valid XML |
| Abstract sentence count | **exactly 5** (verified with a decimal-safe splitter) |
| Acknowledgment phrase + link | exact "Built on the FlyRank ML Internship dataset" → https://flyrank.ai |
| Privacy scan (`portfolio/`, `output/`) | clean — only SVG namespace URLs; no names/domains/queries/secrets |

Interpreter: `C:\Program Files\Python311\python.exe` (pandas 2.3.0, numpy 2.1.3, scikit-learn 1.7.0,
matplotlib 3.10.3); `duckdb 1.5.6`, `reportlab 5.0.1`, `huggingface_hub 2.2.0` added for the warehouse path.

## 6. Key measured results (provenance: committed w03–w07 outputs / `work/outputs/*.json`)

- Lane pool: **100,893 pages × 43 clients** (March 2026 features → April 2026 label, impressions ≥ 100).
- Whole-pool base rate **0.515**; 13 held-out clients base **0.429**.
- Held-out: RF **P@50 0.86** / P@100 0.87 / AUC 0.739 · LR 0.70 / 0.731 · rule **0.44** / 0.451.
- Naive random split P@50 0.92 but **39/39** clients leaked; grouped split 0 overlap.
- GroupKFold(5): P@50 **0.944 ± 0.023**, AUC 0.751 ± 0.024.
- Leak trap (April feature added): P@50 **1.00**, AUC 0.995.
- Top RF driver: momentum (perm. importance 0.1354).
- Queue: investigate_first 1,009 (1.0 %) · watch_prepare 1,514 · monitor 7,567 · no_action 90,803;
  top reason code `ctr_gap+momentum_loss` (714 / 1,009).
- Offline bundled slice (proxy label): rule P@50 0.70 vs LR 0.74 vs RF 0.64; random-split inflation
  P@50 0.98 with 31/31 clients leaked vs 0.64 grouped.

## 7. Honest framing

Observed / measured / directional / decision-support only. No causal refresh claim, no claim about
Google's algorithm, no client/page/query identifiers. The bundled slice's `trend_direction` label is a
same-window proxy and is labelled as such everywhere it appears.

## 8. Remaining items

1. **Warehouse re-run (optional)** — set `HF_TOKEN` to re-execute `w03`–`w07` + the warehouse half of
   `capstone.ipynb` against `FlyRank/internship-warehouse`. Everything else is complete; the committed
   receipts already back every headline number.

_Deployment is done and verified (see §5b). No blockers remain for the capstone submission._
