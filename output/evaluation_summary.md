# Evaluation summary

## Full release (13 held-out clients, seed-42 grouped split, base rate 0.429)

| method | P@10 | P@20 | P@50 | P@100 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| baseline rule | 0.7 | 0.7 | 0.44 | 0.45 | 0.451 |
| logistic regression | 0.8 | 0.85 | 0.7 | 0.69 | 0.731 |
| random forest | 0.9 | 0.9 | 0.86 | 0.87 | 0.739 |

GroupKFold(5): P@50 0.944 +/- 0.023. Leak trap: P@50 1.0, AUC 0.995.

## Bundled slice (offline reproduction, same-window proxy label, held-out clients)

| method | P@10 | P@20 | P@50 | P@100 | ROC-AUC |
|---|---:|---:|---:|---:|---:|
| baseline rule | 0.7 | 0.7 | 0.7 | 0.7 | 0.52 |
| logistic regression | 0.7 | 0.7 | 0.74 | 0.71 | 0.62 |
| random forest | 0.5 | 0.65 | 0.64 | 0.71 | 0.624 |

Random-split contrast: RF P@50 0.98 with 31/31 test clients leaked into train.