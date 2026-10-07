round-4 — Reconstruct

Team: BB-007

Queries used: 148 / 187

What we concluded

GK-05 is a deterministic-looking synthetic building access-control system that maps 10 visible inputs to a continuous score (0.0–1.0) and a binary decision (APPROVE / DECLINE).

We analysed 148 recorded queries. Of these, 147 rows contain all 10 input fields; the final recorded row is incomplete and was excluded from model fitting.

The strongest evidence from the collected data is that the score is not a simple one-feature rule. The strongest observed relationships with score are:

history_score — strongest negative correlation (r ≈ -0.73)

badge_age_days — negative correlation (r ≈ -0.58)

linked_badges — positive correlation (r ≈ +0.54)

recent_denials — negative correlation (r ≈ -0.52)

clearance_level — positive correlation (r ≈ +0.50)

escorts — negative correlation (r ≈ -0.47)

anomaly_ratio has only a weak linear correlation with score in the collected observations (r ≈ -0.09), so it should not be treated as the main driver without further controlled experiments.

The decision output is strongly separated by score in the current dataset. The highest observed DECLINE score is 0.4110, while the lowest observed APPROVE score is 0.4554. This leaves an observed gap between the two classes, so a score threshold is a plausible explanation for the decision. However, the exact threshold has not been proven.

A Random Forest surrogate trained on the 147 complete observations achieved, on one held-out split, approximately MAE 0.042, RMSE 0.103, and R² 0.72 for score prediction. A Random Forest classifier achieved approximately 93.3% accuracy on the held-out decision data. These results show that the visible inputs contain substantial predictive information, but they do not prove that the surrogate has reconstructed the original GK-05 function exactly.

How we got there

1. Consolidated the query history

The CSV contains 148 recorded queries. We used the visible inputs and the returned score/decision as the reconstruction dataset rather than generating synthetic observations.

2. Validated the observations

The expected ten input fields were checked for missing values and duplicates. There are 49 rows involved in duplicate input combinations, but no conflicting score/decision outputs were found among the duplicate complete inputs. This supports treating the system as deterministic for the observed cases.

One final row is incomplete: it has a score and decision but is missing several input fields. It was retained as a record of a query but excluded from supervised model training.

3. Examined the score distribution

There are 138 APPROVE observations and 10 DECLINE observations.

Observed score ranges:

Decision

Minimum score

Maximum score

Mean score

DECLINE

0.0320

0.4110

0.2659

APPROVE

0.4554

0.9934

0.9072

The clean separation is one of the strongest current clues about the decision mechanism.

4. Tested feature relationships

Correlation analysis was used as an exploratory experiment. It identified history_score, badge_age_days, linked_badges, recent_denials, clearance_level, and escorts as the most strongly associated visible variables.

This was useful for prioritising future controlled experiments, but correlation alone was not treated as proof of the underlying implementation.

5. Looked for controlled one-feature changes

We searched the collected observations for pairs that were identical in every visible input except one feature. There were 334 such one-feature pairs across the complete observations.

The strongest average absolute score changes in these matched pairs were associated with:

history_score

badge_age_days

linked_badges

recent_denials

tenure_years

Some controlled pairs also showed zero score change, demonstrating that a feature can have little or no observable effect in a particular context. This is evidence against assuming a simple globally linear contribution from every feature.

6. Investigated the Round-1 controlled sequence

The earlier controlled observations provide particularly useful evidence:

Changing clearance_level from 50 to 100 while keeping the other visible inputs fixed produced the same score: 0.8725 → 0.8725.

Changing history_score from 600 to 900 in the same controlled sequence changed the score substantially: 0.8725 → 0.6449.

Changing badge_age_days from 46.5 to 18 in another controlled comparison produced 0.8213 → 0.8213.

These observations show that the effect of an input cannot safely be inferred from its allowed range alone. Context and/or nonlinear interactions may matter.

7. Built a surrogate model

We trained regression and classification models using the 10 visible inputs, with site one-hot encoded.

The Random Forest regression model was selected as the current practical surrogate because it captures nonlinear relationships and interactions without requiring us to assume a formula.

Feature importance from the fitted surrogate ranked:

history_score

linked_badges

badge_age_days

requested_zone

tenure_years

clearance_level

This ranking is a model-derived clue, not proof of the internal GK-05 implementation.

What we ruled out

A single-feature explanation

The data rules out the idea that one visible input alone explains the observed score. Multiple variables show substantial relationships with score, and the controlled comparisons show different effects in different contexts.

anomaly_ratio as the sole dominant driver

Despite having values across much of its allowed range, anomaly_ratio has only a weak linear correlation with score in the collected dataset. We therefore ruled out treating it as the sole dominant linear driver.

This does not rule out nonlinear or interaction effects involving anomaly_ratio.

clearance_level as a guaranteed direct score driver

The controlled comparison from 50 to 100 produced no score change in one matched context (0.8725 → 0.8725). Therefore, it is not safe to claim that increasing clearance always increases the score.

The overall positive correlation means clearance may still matter in other contexts.

badge_age_days as a simple monotonic rule

A controlled comparison from 46.5 to 18 produced no score change in one context. Therefore, the current evidence does not support a simple rule such as “older badge always increases/decreases score.”

A decision threshold has NOT been ruled out

The observed decision classes are completely separated in the current dataset, so we cannot reject a threshold-based decision rule. The data instead makes a score threshold a strong hypothesis that should be tested with future boundary queries.

What we are still unsure about

Exact score formula — We have not recovered the mathematical function that produces the score.

Exact decision threshold — The current data places the boundary somewhere between 0.4110 and 0.4554, but more targeted queries are required to locate it.

Feature interactions — The controlled experiments suggest context-dependent behaviour, but the exact interactions are unknown.

Role of site — Site-level score means differ, but the current observations do not establish whether site is directly used by the original function or is correlated with other queried values.

Nonlinear effects — Several variables may contain thresholds, saturation, or piecewise behaviour that a simple correlation analysis cannot reveal.

Exact behaviour near the decision boundary — Only 10 DECLINE observations are currently available, so more boundary-focused experiments would be valuable.

Generalisation of the surrogate — The current ML metrics are encouraging, but a good fit on collected observations is not equivalent to an exact reconstruction.

Current reconstruction status

The current evidence supports the following working model:

10 visible inputs → nonlinear score function → likely score-based decision rule

The strongest next experiments should focus on:

locating the exact APPROVE/DECLINE score boundary;

controlled one-variable perturbations around known observations;

testing suspected feature interactions;

testing site effects with matched inputs;

and querying regions where the surrogate has high uncertainty.

The reconstruction is therefore strongly constrained but not yet exact. We avoid claiming rules that the observations do not prove.