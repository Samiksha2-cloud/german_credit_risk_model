# Part 1 — Setup and EDA

## What this covers
Project setup, data understanding, and exploratory data analysis on the
UCI German Credit dataset, as prep for a credit risk classification model.

## Data
- Source: UCI Machine Learning Repository, Statlog (German Credit Data)
- 1000 rows, 20 features, 1 target (`class`: 1 = good credit, 2 = bad credit)
- No missing values, no duplicate rows
- Class balance: 70% good, 30% bad

## Key EDA findings
- `checking_status` is the strongest categorical predictor found: bad-risk
  rate ranges from 11.7% (no checking account) to 49.3% (overdrawn/negative
  balance) — a 37-point spread, far wider than any other feature checked.
- `housing`: applicants who own their home show a lower bad-risk rate (26%)
  than renters (39%) or those in free housing (41%), consistent with
  homeownership as a stability signal.
- `purpose`: some categories showed large swings in bad-risk rate, but
  several had very small sample sizes (e.g. 9-12 applicants) and weren't
  trusted; purposes with solid sample sizes (A40, A41, A43) showed more
  reliable, moderate differences from the 30% baseline.
- `job` showed almost no variation in bad-risk rate across categories
  (28%-34.5%, all close to the 30% baseline) — likely a weak predictor
  on its own.
- `credit_amount` is mildly higher and more variable for bad-risk
  applicants, but the distributions overlap heavily — a weak signal alone.
- `duration` shows a cleaner separation: bad-risk applicants have a higher
  median loan term (24 vs 18 months) and much more spread, including loan
  terms up to 72 months with no flagged outliers (vs. class 1, where terms
  above ~42 months are flagged as unusual).
- `duration` and `credit_amount` are moderately correlated (0.62) —
  larger loans tend to have longer terms, a collinearity to watch in
  modeling.
- No numeric column is strongly correlated with `class` alone (all below
  0.25); checking_status (categorical, strongest finding) isn't captured
  in this correlation check at all, since it only covers numeric columns.

## Files in this folder
- `german_credit_raw.csv` — raw dataset (UCI source)
- `eda.ipynb` — notebook with setup, data dictionary, and EDA
