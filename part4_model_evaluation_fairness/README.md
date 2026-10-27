# Part 4 — Models, Evaluation, Explainability and Fairness

## Model comparison (5-fold x 3-repeat cross-validation, 800 training rows)
| Model | Held-out ROC-AUC | Train ROC-AUC |
|---|---|---|
| Dummy (always "good") | 0.50 | n/a |
| Logistic regression (balanced) | 0.779 (spread 0.043) | 0.836 |
| Random forest, small | 0.782 | 0.861 |
| Random forest, large | 0.791 | 0.929 |
| XGBoost (shallow) | 0.793 | 0.913 |

All real models score about 0.78 to 0.79, which is within fold-to-fold
noise (about 0.04). I chose logistic regression because it overfits least
and is the easiest to explain.

## Decision threshold
Cost rule from the UCI data: approving a bad applicant costs 5x more than
rejecting a good one. On cross-validated training predictions, the cutoff of
0.3 had the lowest cost (425, versus 538 at the default 0.5). Costs between
0.2 and 0.35 were close, so the exact value is not critical.

## Test results (200 rows, 60 bad applicants)
| | Recall | Precision | ROC-AUC | Cost |
|---|---|---|---|---|
| First look (model with all features) | 0.85 | 0.425 | 0.81 | 114 |
| Final model (second look, no `personal_status`) | 0.87 | 0.43 | 0.82 | 109 |

- 95% bootstrap interval for the first test ROC-AUC: 0.74 to 0.88.
- For comparison, rejecting everyone costs 140 and approving everyone 300.
- The second look is labelled as such. The decision to drop the feature was
  made on training data only, and nothing was changed after the test score.

## Explainability
Logistic regression coefficients show the direction of each feature's
effect. For one rejected applicant, each feature's contribution was
calculated as coefficient x (value - average value). Example: a borderline
rejection (chance of bad 0.34 versus cutoff 0.30) was driven by other
installment plans and checking balance, offset by a large savings balance.

## Fairness check (cutoff 0.3, cross-validated predictions)
| | Rejected | Good applicants wrongly rejected |
|---|---|---|
| Female (with `personal_status`) | 68% | 56% |
| Male (with `personal_status`) | 56% | 45% |
| Female (final model) | 61% | 47% |
| Male (final model) | 60% | 50% |

- Removing the sex and marital-status column cost 0.005 AUC (within noise)
  and closed the gap between women and men.
- Young applicants (18-25) are still rejected most (79%, with 71% of good
  young applicants wrongly rejected) because `age` features remain.
  Their real bad rate is higher (41% versus 27-28%), but the burden is large.

## Limitations
- Only 1000 rows, and 60 bad applicants in the test set, so all estimates
  are noisy.
- Exploratory analysis used all 1000 rows, including the later test rows (a
  mild leak).
- The 5:1 cost ratio is assumed, and the chosen cutoff depends on it. At
  0.3 the model rejects about half of the good applicants.
- Sex is only available combined with marital status (`personal_status`),
  and the only female category is divorced, separated or married.
- Age and `foreign_worker` (a nationality proxy) are still used. Other
  features may act as proxies for removed ones.
- Probabilities were not calibrated.
- Some reasons are statistically right but odd (a "critical account" history
  lowers predicted risk).
- Old (1990s German) data, not representative of current lending.

## Files
- `modeling_evaluation_fairness.ipynb` — notebook
- `credit_risk_pipeline.joblib` — final pipeline (expects the same 23 input
  columns; use cutoff 0.3)
