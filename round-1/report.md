---

# round-1 — Observe

**Team:** BB-007

**Queries used:** 36 / 150

## What we concluded

The access control system `GK-05` evaluates badge requests using a synthetic scoring model that rewards clean risk metrics (`anomaly_ratio` and `recent_denials` set to $0$) and maximum `clearance_level` ($100$). Crucially, the model exhibits **inverted domain logic**: unlike real-world security systems where high history scores and old badges build trust, `GK-05` penalizes high historical scores (driving scores down to `DECLINE`) and heavily rewards a minimized `history_score` ($300$) paired with lower `badge_age_days` ($20$). Categorical site selection also impacts performance, with Site `B` achieving our peak score of **0.9651** (`APPROVE`) on Query #36.

## How we got there

1. **Baseline & Exploratory Probing (Queries 1–15):** Tested broad parameter sweeps across anomaly ratios, escorts, and history scores to identify which fields triggered major score movements.


2. **Discovering Inverted Weights (Queries 16–28):** Realized that dropping `history_score` down to its minimum bound ($300$) dramatically increased approval scores, contrary to initial assumptions.


3. **Fine-Tuning Secondary Variables (Queries 29–35):** Systematically adjusted `badge_age_days` ($20$), `tenure_years` ($20$), and `linked_badges` to find stable upper-bound plateaus in the mid-$0.95$ range.


4. **Site Optimization (Query #36):** Switched the categorical `site` parameter to `B` and adjusted `linked_badges` to $11$, successfully unlocking our highest score of **0.9651**.



## What we ruled out

* **Traditional Domain Intuition:** We hypothesized that maximizing `history_score` ($900$) and `badge_age_days` ($75$) would yield the highest score. This was completely ruled out when Query #27 set `history_score` to $900$, resulting in a steep drop to a `DECLINE` decision ($0.3338$).


* **Tolerance for Anomalies:** We tested non-zero values for `anomaly_ratio` and `recent_denials`, which confirmed that any deviation from $0$ instantly introduces a sharp penalty to the confidence score.



## What we are still unsure about

* **Exact Mathematical Coefficients:** While we know minimizing history and maximizing clearance are key, the exact linear or non-linear weights governing intermediate variables like `requested_zone` and `tenure_years` require deeper regression analysis.
* **The Upper Bound Limit:** We are uncertain whether a score of $0.9999$ or $1.0$ is mathematically reachable for system `GK-05` with these specific feature constraints, or if the model architecture imposes a hard ceiling below $1.0$ for this configuration.