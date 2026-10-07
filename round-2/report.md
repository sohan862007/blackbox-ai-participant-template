# round-2 — Investigate

**Team:** BB-007
**Queries used:** 107 / 150

## What we concluded

In Round 2, we successfully pushed our peak confidence score to an exceptional **0.9934** (`APPROVE`) at Query #71[cite: 10]. By shifting our site selection to `B`, tightening the `requested_zone` down to $32$, and carefully calibrating fractional risk metrics (`anomaly_ratio = 0.925` and `recent_denials = 1.25`), we unlocked a much higher performance tier in the `GK-05` access control system[cite: 10, 12].

## How we got there

1. **Parameter Re-calibration (Queries R2-38 to R2-70):** Explored localized variations around our previous high watermark, testing how minor adjustments to zone bounds and clearance levels affect model stability.
2. **Site Shift & Risk Vector Optimization (Query #71):** Implemented a strategic shift from site `A` to site `B`, lowered `requested_zone` to $32$, reduced `clearance_level` to $90$, set `escorts` to $0$, and adjusted `anomaly_ratio` to $0.925$ with `recent_denials` at $1.25$[cite: 10, 12].
3. **Peak Score Attainment:** These precise adjustments drove an immediate score increment up to **0.9934** (`APPROVE`)[cite: 10].

## What we ruled out

* **Site A Exclusivity:** We ruled out the assumption that site `A` is strictly required for near-perfect scores, as transitioning to site `B` under our current tuned parameter set yielded superior results[cite: 10, 12].
* **Zero-Tolerance Denial Constraints:** We disproved the hypothesis that `recent_denials` must remain rigidly at zero or low integer values; a fractional denial weight of $1.25$ actively contributed to the higher confidence output[cite: 10, 12].

## What we are still unsure about

* **The Final Threshold to 1.0:** While a score of $0.9934$ sits right on the edge of perfection, the exact mathematical vector or micro-adjustment required to cross completely into a $1.0$ confidence rating remains to be fully mapped[cite: 10].