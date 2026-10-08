# Part 2 — Feature Engineering

## What this covers
Deciding how to encode each column, and building new features from the
patterns found in EDA (Part 1).

## Encoding decisions
- **Ordinal encoding** (categories have a defensible low-to-high order):
  `checking_status`, `savings_status`, `credit_history`, `employment`, `job`
- **One-hot encoding** (no natural order, or the order is debatable):
  `purpose`, `personal_status`, `other_parties`, `property_magnitude`,
  `other_payment_plans`, `housing`, `own_telephone`, `foreign_worker`
- `property_magnitude` was kept as one-hot because it is debatable whether
  life insurance really ranks above a car, and forcing an order could mislead
  the model.

## Engineered features
- **monthly_repayment** = credit_amount / duration. Shows how much the
  applicant has to pay each month. `duration` and `credit_amount` are
  correlated (0.62), so neither alone answers this question.
- **age_band**: exact age grouped into young / adult / middle_aged / senior.
  Bad-risk rate was 42% for young (n=190) versus 22-28% for the other bands,
  so this feature was kept. The senior band (n=45) is small, so treat its
  22% with caution.
- **log_credit_amount**: log of credit_amount. The boxplot showed it is
  right-skewed, and the log compresses large values more than small ones.
- **affordability_pressure** = installment_commitment x monthly_repayment.
  Tested with a boxplot by class: the boxes overlapped almost completely, so
  no separation was found. Tried and not kept.

## Files in this folder
- `feature_engineering.ipynb` — notebook with the feature engineering code
