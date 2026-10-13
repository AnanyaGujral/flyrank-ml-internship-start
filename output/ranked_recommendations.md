# Ranked recommendations — the action playbook

Decision-support ranking of already-visible pages for human review. Ranking quality is measured on whole held-out clients; tiering is a review convention.

- Pool: **18,010 pages** across 30 pseudonymized clients
- Whole-pool decline base rate: 0.616
- Held-out clients: rule P@50 0.76, forest P@50 0.64 (base 0.567)

| action | pages | what it means |
|---|---:|---|
| investigate_first | 1,801 | top 10% — editor opens these this week |
| watch_prepare | 2,709 | next 15% — queue behind the top tier |
| monitor | 3,127 | next 25% — light check, no rewrite |
| no_action | 10,373 | healthy or out of scope |

## Top reason codes (review-first tier)

- `ctr_gap+no_clicks` — 881 pages
- `ctr_gap+high_exposure` — 577 pages
- `ctr_gap+no_clicks+high_exposure` — 191 pages
- `ctr_gap` — 152 pages

## Limits

- One snapshot; the label is a window comparison, so seasonal pages can look like decliners.
- Scores are rankings, not calibrated probabilities.
- Never automate the action: the queue orders a human review (see `w07_action_playbook.ipynb`).