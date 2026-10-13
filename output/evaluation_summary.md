# Evaluation summary

Offline study on the bundled anonymized slice — `data/raw/content_refresh_anonymized.csv` (30,000 pages, 32 clients). Pool 18,010 pages. Label: later-30 impressions < 80% of prior-30. Features: prior-30 window + metadata only.

Validation: whole clients held out (seed 42, 30% of clients); base rate 0.567.

| method | P@10 | P@20 | P@50 | P@100 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| transparent rule | 0.700 | 0.700 | 0.760 | 0.670 | 0.580 |
| logistic regression | 0.700 | 0.750 | 0.760 | 0.740 | 0.585 |
| random forest | 0.500 | 0.500 | 0.640 | 0.600 | 0.582 |

GroupKFold(5) P@50: forest 0.788 ± 0.148; rule 0.748 ± 0.126.

Invalid designs: random split forest P@50 0.96 with 27 clients leaked; leak trap P@50 1.0 / AUC 0.864.

**Honest reading:** the trailing features carry little signal about next-window decline; the transparent rule is at least as good as the learned models, and naive validation makes the model look far better than it is.