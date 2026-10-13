# Capstone Report — Refresh / Content Opportunity Scoring (Lane 2)

- **Author:** Ananya Gujral
- **Lane:** 2 — Refresh / Content Opportunity Scoring
- **Repo:** https://github.com/AnanyaGujral/flyrank-ml-internship-start
- **Date:** 2026-10-14
- **Paper:** https://ananyagujral.github.io/flyrank-ml-internship-start/

> This is the write-up the eight rubric axes are graded on. Sections 0 and 9 are also paper
> sections and appear verbatim (abstract) or in substance (data credit) on the deployed page.
> Every number below is produced by the committed notebooks and receipts in
> `work/outputs/*.json`, on the bundled offline slice.

## 0. Abstract

A content editor can review only a few hundred of a portfolio's already-visible pages a week, so I
asked whether trailing search and engagement signals can rank next-window decline better than a
transparent rule, measured at the top of the queue under client-grouped validation. Working offline on
the bundled anonymized FlyRank slice (30,000 pages across 32 pseudonymized clients), I defined the label
from the data's own two stacked 30-day windows — later-30 impressions below 80% of prior-30 — and used
only prior-window features plus static metadata, scoring a transparent rule, logistic regression, a
random forest and gradient boosting on 9 held-out clients (3,571 pages, base rate 0.567). At the top of
the queue the transparent rule reached precision@50 0.76 and logistic regression tied it at 0.76, a
rule-plus-model blend edged to 0.78, while the tree models did worse (0.58–0.64), and five-fold
client-grouped stability put the forest at 0.788 ± 0.148 against the rule's 0.748 ± 0.126 — no model
clearly won. Discrimination stayed weak overall (ROC-AUC 0.58–0.61 against 0.50 for chance), and two
invalid designs inflated the numbers dramatically: a random page split that leaked 27 of 28 test
clients into training lifted the forest to precision@50 0.96, and adding any single label-window column
pushed ROC-AUC to 0.864–1.000. The honest conclusion is that on this snapshot the trailing signals
carry little ranking power and the transparent rule is a strong default, so the capstone ships a
human-reviewed four-tier queue ranked by the rule and documents the learned models as no better rather
than as a win.

## 1. Problem framing

- **Decision it supports:** with a fixed weekly review budget, which already-visible pages should an
  editor open first?
- **Unit of analysis:** one content page (a row of the slice), grouped by `client_id` for validation.
- **Output:** a priority score and a rank — an ordered queue — over the pages that are already being
  seen, plus a feature-only reason code per page.
- **Action a human takes:** work down the queue, inspect the flagged page (real drop or seasonality?),
  and decide refresh / watch / leave.
- **Cost of a wrong call:** wasting scarce editor hours on healthy pages (false positives) or missing a
  page that is quietly decaying (false negatives). At a few hundred reviews a week against tens of
  thousands of pages, allocation *is* the problem.
- **Why data/ML helps at all:** the portfolio already has real impressions, clicks, position and
  engagement for every page, so a ranking can be learned or hand-written from evidence instead of
  gut feel — provided the design is honest. This is deliberately a **ranking/scoring** task, not
  classification: the metric that matters is precision at the *top* of the queue, not overall accuracy
  (which the tens of thousands of never-opened rows would dominate).

## 2. Data safety

- **Data used:** the single anonymized file that ships with the repo,
  `data/raw/content_refresh_anonymized.csv` — 30,000 rows × 44 columns, 32 pseudonymized clients, one
  trailing-90-day snapshot holding two stacked 30-day windows. Analysis runs **offline**; the gated
  `FlyRank/internship-warehouse` release is not used and was not reachable (HTTP 401).
- **Decision window vs outcome window:** features come from the prior-30 window (`*_prev_30d`, days
  31–60) plus static metadata; the label comes from the later-30 window (`*_last_30d`, days 1–30).
- **Columns deliberately excluded from the feature matrix (and why):**
  - `trend_direction`, `trend_pct` — **label-derived**; the label reproduces
    `trend_direction == "down"` for 100% of pool rows.
  - every `*_last_30d` column — they *are* the outcome window.
  - all `*_90d` totals and their rates (`ctr`, `avg_position`, `engagement_rate`, `scroll_rate`,
    `ai_traffic_pct`, coverage days) — they overlap the outcome window.
  - `impression_tier`, `position_tier` — bucketed from those same overlapping totals.
  - production metadata (`provider_used`, `model_used`) — operational, not a page property.
  - identifiers (`content_id`, `client_id`) — **grouping only, never features**.
- **Leakage checks run:** a probe adding one label-window ratio takes ROC-AUC from an honest **0.579**
  to **1.000** (`+momentum`) and **1.000** (`+trend_pct`); adding the outcome window gives **0.848** —
  all reported in `work/outputs/w03_feature_leakage_metrics.json`.
- **Public safety:** nothing client-identifying (no names, domains, URLs, queries, tokens) appears
  anywhere in `work/`, `output/`, or `portfolio/`. Confirmed by scan before publishing.

## 3. Baseline

The baseline is a transparent rule any editor can read and defend:

```
score = (ctr_prev ≤ 0.5 × pool_median_ctr) × log1p(impressions_prev_30d)
```

i.e. *a page is worth reviewing when it is visible but under-earning clicks, and the bigger its
prior-window exposure the higher it ranks.* Pool median prior CTR is **0.114** (percent scale). It
emits feature-only reason codes (`ctr_gap`, `no_clicks`, `high_exposure`).

Why it's a fair comparison: it uses only prior-window features, is computed on the same pool and the
same held-out clients, and is fixed before any model is trained. On the same 9 held-out clients
(3,571 pages, base rate 0.567) it scores **P@10 0.70 · P@20 0.70 · P@50 0.76 · P@100 0.67 · ROC-AUC
0.580**. Any model must beat *that* on the same split — not a weaker straw man.

## 4. Model / analysis

- **Methods:** logistic regression (interpretable default), random forest (500 trees, leaf size 10),
  histogram gradient boosting (300 iterations). A rule-plus-model blend is reported as a fourth row to
  test complementarity. All `random_state=42`.
- **Features (43 columns after one-hot encoding):** prior-30 impressions/clicks/sessions (raw + log),
  prior-30 CTR and clicks-per-session, CTR normalised against the page's own client (`ctr_prev_z`),
  the page's share of its client's prior-window demand, keyword context (`search_volume`,
  `competition`, `cpc`), depth (`word_count`, `char_count`), age/freshness, presence flags, and
  categorical descriptors. Missing numerics are filled to zero and paired with presence flags, because
  missingness tracks `content_type` rather than chance.
- **Target / proxy, one sentence:** `declined_next_30d = impressions_last_30d < 0.8 ×
  impressions_prev_30d` — a defined window-comparison rule, equal to the slice's own
  `trend_direction == "down"`, not an externally observed business outcome.
- **Left out on purpose:** everything overlapping the outcome window (see §2) — the whole point of the
  design.

## 5. Evaluation

- **Split:** whole clients are held out with `GroupShuffleSplit` (30% of clients, `random_state=42`) →
  **9 unseen clients / 3,571 pages / base rate 0.567**. Stability uses five-fold `GroupKFold` over
  clients. A random *page* split and a leak trap are run deliberately as **invalid** controls.
- **Model vs baseline, same split:**

  | method | P@10 | P@20 | P@50 | P@100 | ROC-AUC |
  |---|---:|---:|---:|---:|---:|
  | transparent rule | 0.70 | 0.70 | **0.76** | 0.67 | 0.580 |
  | logistic regression | 0.70 | 0.75 | **0.76** | 0.74 | 0.585 |
  | rule + best model blend | 0.70 | 0.75 | **0.78** | 0.75 | 0.610 |
  | random forest | 0.50 | 0.50 | 0.64 | 0.60 | 0.582 |
  | hist gradient boosting | 0.40 | 0.55 | 0.58 | 0.59 | 0.582 |

  Base rate **0.567** — reported next to every precision figure, because a high score can just be a
  high base rate. (It is: P@50 = 0.76 is only +0.19 over base, and AUC ≈ 0.58 is barely over chance.)
- **Stability:** GroupKFold(5) P@50 — forest **0.788 ± 0.148** vs rule **0.748 ± 0.126**; the fold
  spread overlaps completely.
- **Error analysis (short):** the forest's top-50 picks share only **1 page** with the rule's top-50 —
  it is ranking a *different* set of pages, and on held-out clients that different set is not better.
  The two invalid designs show where the illusion comes from: the random split leaks **27 of 28** test
  clients into training and lifts forest P@50 to **0.96**; the leak trap reaches **P@50 1.00 / AUC
  0.864**.

## 6. Interpretation

- **What the models actually found:** a permutation-importance check on the forest puts page age
  (`content_age_days`, 0.023) and client-relative CTR (`ctr_prev_z`, 0.020) on top — the same
  ingredients the rule already encodes.
- **The one clean signal:** prior-window **CTR** is monotone on ≥500-impression pages — bottom third
  declines 72.2%, middle 60.3%, top 49.9%. Prior-window **volume** is *not* monotone (low 62.3%,
  middle 65.2%, high 57.3%): exposure makes a page matter, not risky.
- **Negative result (a valid result):** on whole unseen clients **no learned model reliably beats the
  transparent rule**. That is a well-understood "no effect" — the trailing signals in this snapshot
  carry little information about next-window decline, and no amount of model complexity recovers
  information the data does not hold.
- **Surprise:** how fragile the headline is. The same forest reads 0.96 or 0.64 depending only on
  whether the split respects client boundaries.

## 7. Recommendation

- **The deliverable is a single ranked queue** of all 18,010 pool pages, each with a reason code and a
  four-tier action:

  | action | pages | what an editor does |
  |---|---:|---|
  | `investigate_first` | 1,801 | top 10% — open these this week |
  | `watch_prepare` | 2,709 | next 15% — queue behind the top tier |
  | `monitor` | 3,127 | next 25% — light check, no rewrite |
  | `no_action` | 10,373 | healthy or out of scope |

  The review-first tier carries **15.7%** of the pool's prior-window impressions; the most common
  reason codes there are `ctr_gap+no_clicks` (881), `ctr_gap+high_exposure` (577), and
  `ctr_gap+no_clicks+high_exposure` (191).
- **How a FlyRank editor uses it tomorrow:** filter to `investigate_first`, work top-down, and for each
  page check whether the low CTR is real or deliberate-intent, whether a campaign/seasonality window
  explains it, and whether the change is genuine — then refresh or dismiss.
- **Confidence and limits:** the *ranking* is measured on whole held-out clients (rule P@50 0.76 vs
  base 0.567) and is directional, not calibrated; the queue is decision support for a human review.
  Four things are **never** automated: rewriting content, pruning pages, acting on a score with no
  review, and treating the score as a traffic forecast. **Monitoring:** pause and re-check if the
  pool decline base rate leaves **0.566–0.666**, or if a re-run drops held-out P@50 by more than 0.10.

## 8. Reproducibility

- **From a fresh clone:**
  ```bash
  pip install -r requirements.txt
  python scripts/run_all.py        # starter pipeline on the bundled 30k slice (the reference baseline)
  ```
  then run `work/notebooks/w01…w07` and `work/notebooks/capstone.ipynb` top to bottom (Colab or a
  local Jupyter kernel). No warehouse token is needed — the study is entirely offline.
- **Seeds:** `random_state=42` everywhere; `GroupShuffleSplit(test_size=0.30)` by `client_id`;
  `GroupKFold(n_splits=5)`.
- **Environment:** Python 3.11, pandas 2.3.0, numpy 2.1.3, scikit-learn 1.7.0, matplotlib 3.10.3.
- **Sealed/holdout evaluation is checkable, not on faith:** the cell that builds the client-grouped
  holdout lives in `work/notebooks/w05_model.ipynb` (and the GroupKFold/leak controls in
  `work/notebooks/w06_validation_audit.ipynb`); the metrics those cells produced are committed as
  `work/outputs/w05_model_metrics.json` and `work/outputs/w06_validation_metrics.json`. The remaining
  receipts are `work/outputs/w03_data_contract_metrics.json`,
  `w03_feature_leakage_metrics.json`, `w04_baseline_metrics.json`, `w04_signal_audit_metrics.json`,
  `w07_playbook_metrics.json`.
- **Paper-facing artifacts** (regenerated by `capstone.ipynb`): `output/evaluation_summary.md`,
  `output/data_quality_summary.md`, `output/ranked_recommendations.md`,
  `output/capstone_results.json`, `output/charts/*.svg` (mirrored to `portfolio/assets/`).

## 9. Acknowledgments & data credit

Built on the FlyRank ML Internship dataset — [flyrank.ai](https://flyrank.ai). Thanks to the FlyRank
team for the anonymized slice, the data dictionary and the lane guide. All analysis, code, errors and
limitations are my own.

---

**Claims checklist:** observed / measured / directional / decision-support language throughout · no
causal refresh claim · no "predicted Google's algorithm" · base rate reported next to every precision@K
· no client-identifying details · numbers match a fresh re-run.
