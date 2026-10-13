# Ranked recommendations (full release, decision-support)

Review-first tier: 1,009 pages = 1.0% of the pool covering 0.8% of March demand (80.7% of them on page one).

| action | pages | share of pool | what the editor does |
|---|---:|---:|---|
| investigate_first | 1,009 | 1.0% | first pass; read the reason code and review by hand |
| watch_prepare | 1,514 | 1.5% | draft a refresh while evidence matures |
| monitor | 7,567 | 7.5% | drift-watch only |
| no_action | 90,803 | 90.0% | leave it |

| reason code (review-first tier) | pages |
|---|---:|
| ctr_gap+momentum_loss | 714 |
| momentum_loss | 285 |
| momentum_loss+volume_exposure | 7 |
| base_anchor | 1 |
| volume_exposure | 1 |
| ctr_gap+volume_exposure | 1 |

Reason codes are feature-only flags (CTR gap vs the page's position band, lost momentum, inactivity, volume exposure). The most common is `ctr_gap+momentum_loss`: a page one that is both under-earning clicks and already decelerating.