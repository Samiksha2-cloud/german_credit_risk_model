# Part 3 — Train/Test Split and Validation Strategy

## What this covers
Setting up an honest evaluation before any real model is trained.

## Decisions
- **Split:** 80/20 stratified (800 train / 200 test, random_state=42). A
  random split is correct here because the data has no time column.
  Bad-risk rate is 30% in both parts. The test set is used once, at the end.
- **Leakage check:** both `class` and `bad_risk` (the target in two
  encodings) were removed from the features before splitting.
- **Validation:** RepeatedStratifiedKFold, 5 folds x 3 repeats = 15 fits per
  model, with a fixed seed so every model is scored on identical folds.
- **Encoding:** ordinal for `checking_status`, `savings_status`, `employment`
  and `age_band` (ordered by training bad rate, bigger number = higher risk).
  One-hot for the other 10 categoricals. `credit_history` and `job` were
  first planned as ordinal but moved to one-hot, because training bad rates
  contradicted the meaning order.
- **Pipeline:** the preprocessor sits inside a sklearn Pipeline, so encoders
  are fit on each fold's training part only.
- **Baseline:** a dummy model that always predicts "good" scores 0.70
  accuracy and 0.5 ROC-AUC. Real models must beat this clearly.

## Limitation
EDA in Part 1 used all 1000 rows, including the rows later held out as the
test set. The effect is small, but it is a mild leak.

## Files in this folder
- `split_and_validation.ipynb` — notebook
