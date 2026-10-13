# PROGRESS — FlyRank ML Internship Capstone

_Last updated: 2026-10-14_

**Owner:** Ananya Gujral · **Repo:** https://github.com/AnanyaGujral/flyrank-ml-internship-start
**Track:** Machine Learning · **Lane:** 2 — Refresh / Content Opportunity Scoring

---

## 0. Provenance note (honest rebuild)

An earlier version of `work/notebooks/w02…w07` and `capstone.ipynb` carried a **reference repo's**
content — its numbers, charts and receipts (P@50 0.86, AUC 0.739, 100,893 rows) — which is not my work
and not reproducible here. The environment has **no access** to the gated
`FlyRank/internship-warehouse` dataset (anonymous access returns HTTP 401; no token was issued), so
those numbers could not have been produced by me anyway.

I therefore **rebuilt every notebook from scratch** (`w01`–`w07` + `capstone`) so that all analysis is
genuinely re-derived on my machine from the one dataset that ships with the repo,
`data/raw/content_refresh_anonymized.csv`. All receipts, charts, tables and the deployed paper now come
from that offline run. The reference repo's ideas (receipts, an executable paper-mirror notebook) were
reused; **none of its files, numbers, charts or conclusions were**.

## 1. Audit summary (state now)

| Area | State |
|---|---|
| `work/notebooks/w01_research_question.ipynb` (ML-02) | ✅ **rebuilt + executed offline** |
| `work/notebooks/w02_ml_task_framing.ipynb` (ML-03) | ✅ **rebuilt + executed offline** |
| `work/notebooks/w03_data_contract.ipynb` (ML-04) | ✅ **rebuilt + executed offline** |
| `work/notebooks/w03_feature_leakage_check.ipynb` (ML-05) | ✅ **rebuilt + executed offline** |
| `work/notebooks/w04_signal_audit.ipynb` (ML-06) | ✅ **rebuilt + executed offline** |
| `work/notebooks/w04_baseline_score.ipynb` (ML-07) | ✅ **rebuilt + executed offline** |
| `work/notebooks/w05_model.ipynb` (ML-08) | ✅ **rebuilt + executed offline** |
| `work/notebooks/w06_validation_audit.ipynb` (ML-09) | ✅ **rebuilt + executed offline** |
| `work/notebooks/w07_action_playbook.ipynb` (ML-10) | ✅ **rebuilt + executed offline** |
| `work/notebooks/capstone.ipynb` (ML-11/12) | ✅ **rebuilt + executed offline** |
| `work/outputs/*.json` (receipts) | ✅ **regenerated offline** from the rebuilt notebooks |
| `outputs/` (starter reference) | unchanged (reverted after a validation run) |
| `output/` | ✅ regenerated from the new receipts (4 charts, 3 summaries, `capstone_results.json`, `README.md`) |
| `portfolio/` | ✅ the research paper, rebuilt around the offline numbers |
| `submission/paper_url.txt` | ✅ deployed URL written |
| Root `README.md` | ✅ capstone section updated to the offline results |

## 2. Chosen lane and research question

- **Lane 2 — Refresh / Content Opportunity Scoring.**
- **Question:** *Among a client portfolio's already-visible pages, can trailing search and engagement
  signals rank the pages that are about to decline well enough to beat a transparent rule — measured at
  the top of the queue, under a client-grouped (new-client) validation design?*

## 3. Data & access

- **Source of truth (offline):** `data/raw/content_refresh_anonymized.csv` — 30,000 pages × 44 columns,
  32 pseudonymized clients, one trailing-90-day snapshot holding **two stacked 30-day windows**.
- **Decision window:** prior-30 (days 31–60). **Outcome window:** later-30 (days 1–30).
- **Gated warehouse (`FlyRank/internship-warehouse`): not reachable** (HTTP 401, no token). The whole
  study runs without it; nothing here depends on it.

## 4. Task checklist

- [x] Local repo + git audit
- [x] Chosen lane + research question documented
- [x] Rebuilt all 10 notebooks on the offline slice (w01–w07 + capstone)
- [x] Executed all 10 notebooks top to bottom — 0 error cells, outputs saved
- [x] Regenerated the 7 receipts in `work/outputs/*.json`
- [x] Regenerated `/output` artifacts from the receipts
- [x] Rebuilt `/portfolio` research paper (9 sections, 5-sentence abstract, flyrank.ai credit)
- [x] Updated root `README.md` + reproduction instructions
- [x] Privacy / publication review (see §6)
- [x] GitHub Pages deployment config in place (`.github/workflows/deploy-pages.yml`)
- [x] Push to `origin/main`
- [x] Deploy + re-verify live URL → `submission/paper_url.txt`

## 5. Deployment

- **Live paper:** https://ananyagujral.github.io/flyrank-ml-internship-start/
- Served from `portfolio/` via `.github/workflows/deploy-pages.yml` (Pages source = GitHub Actions).
- Re-verified after the honest rebuild: paper returns HTTP 200 with the new offline title/abstract, all 9
  section anchors, the exact "Built on the FlyRank ML Internship dataset" credit linking to
  https://flyrank.ai, and the four new chart assets (`assets/*.svg`).
- `submission/paper_url.txt` holds exactly that one URL.

## 6. Validation performed

| Check | Result |
|---|---|
| All 10 notebooks executed top to bottom (offline) | ✅ 0 error cells each |
| Notebook CI condition (`code>200 chars ⇒ has outputs`) | ✅ satisfied |
| Receipts rewritten offline | ✅ 7 files; old borrowed receipts gone |
| Stale chart artifacts removed | ✅ `feature_importance.svg` / `split_comparison.svg` (old run) deleted |
| Abstract sentence count | ✅ exactly 5 (decimal-safe splitter) |
| Acknowledgment phrase + link | ✅ exact "Built on the FlyRank ML Internship dataset" → https://flyrank.ai |
| Privacy scan (`portfolio/`, `output/`) | ✅ clean — only SVG namespace URLs; no names/domains/queries/secrets |
| Leak-awareness | ✅ outcome-window / 90-day / trend / tier columns excluded from features |
| Determinism | ✅ seed 42 everywhere; client-grouped split |

## 7. Key measured results (offline · provenance: `work/outputs/*.json`)

- **Slice:** 30,000 rows × 44 cols, 32 clients. **Pool** (`impressions_prev_30d ≥ 100`): **18,010 pages
  × 30 clients** (60.0 %), base rate **0.616**.
- **Label:** `declined_next_30d = impressions_last_30d < 0.8 × impressions_prev_30d` (= the slice's own
  `trend_direction == "down"`, 100 % agreement).
- **Held-out clients** (seed 42, 9 clients, 3,571 pages, base **0.567**):

  | method | P@10 | P@20 | P@50 | P@100 | ROC-AUC |
  |---|---:|---:|---:|---:|---:|
  | transparent rule | 0.70 | 0.70 | **0.76** | 0.67 | 0.580 |
  | logistic regression | 0.70 | 0.75 | **0.76** | 0.74 | 0.585 |
  | rule + best model blend | 0.70 | 0.75 | **0.78** | 0.75 | 0.610 |
  | random forest | 0.50 | 0.50 | 0.64 | 0.60 | 0.582 |
  | hist gradient boosting | 0.40 | 0.55 | 0.58 | 0.59 | 0.582 |

- **GroupKFold(5) P@50:** forest **0.788 ± 0.148** vs rule **0.748 ± 0.126** → indistinguishable.
- **Invalid designs:** random page split → forest P@50 **0.96** with **27/28** clients leaked (grouped
  0.64); leak trap (+ one label-window column) → P@50 **1.00**, AUC **0.864**. Feature leak probe:
  honest AUC 0.579 → +momentum 1.000 → +`trend_pct` 1.000 → +outcome window 0.848.
- **Signal audit:** prior CTR is the one clean monotone driver (bottom third declines 72.2 %, middle
  60.3 %, top 49.9 % on ≥500-impression pages). Volume is **not** monotone. Flag lifts:
  `low_ctr_visible` +0.144 (n = 2,772), `no_clicks_visible` +0.136 (n = 2,043), `stale_visible` +0.384
  (n = 11), `thin_visible` +0.064 (n = 25).
- **Queue:** investigate_first **1,801** · watch_prepare **2,709** · monitor **3,127** · no_action
  **10,373**; review-first carries 15.7 % of pool impressions; top reason codes `ctr_gap+no_clicks`
  (881), `ctr_gap+high_exposure` (577), `ctr_gap+no_clicks+high_exposure` (191).

**Honest reading:** on this snapshot the trailing signals carry little ranking power; the transparent
rule is at least as good as every learned model, and naive validation inflates the model to 0.96. The
model is documented as an **unmatched alternative**, not a win.

## 8. Honest framing

Observed / measured / directional / decision-support only. No causal refresh claim, no claim about
Google's algorithm, no client/page/query identifiers. Global discrimination is weak (ROC-AUC ≈ 0.58), so
the output is a **top-of-queue ordering**, not a calibrated probability. The label is a defined
window-comparison rule, labelled as such everywhere it appears.

## 9. Remaining items

- None blocking. An optional future step is to re-run against the gated warehouse on a broader time
  range; it is not required for this submission and every headline number is reproducible offline today.

_Deployment is live and re-verified; no blockers remain for the capstone submission._
