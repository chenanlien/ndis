# Ahn vs Amangku Prediction Models

## Purpose

This document summarizes a manual comparison of the Ahn and Amangku prediction-model code.

## 1. Prediction variables

| Component | Ahn | Amangku | Difference |
|---|---|---|---|
| Prediction target | `ndis_ever`; `ndis_2year` | `ndis_ever`; `ndis_2year` | Both targets have been used in both models. |
| AEDC | Included | Variables beginning with `aedc_` are excluded | Only Ahn uses the AEDC predictors. |
| MBS | Item dummies, subgroup variables, and specialty variables | Raw-item ever-use indicators | Ahn uses additional MBS feature groups. |
| PBS | ATC variables, clinical categories, category-by-age variables, observation-age indicators, and selected ATC5 dummies | Raw-item ever-use indicators | The models represent PBS history differently. |
| Parent DOMINO | Included | Included | Same upstream construction. |
| Parent income | Three pre-rollout household income measures and `par_avg_inc` are constructed | `par_avg_inc` is converted into `income_bucket_*` dummies | Check the final Ahn `xvar` to determine whether it also uses income buckets. |
| Child age | Check the final Ahn `xvar` | Rounded and converted into `age_round_*` dummies | Check whether Ahn uses continuous age, age dummies, or both. |
| Location | `lga_code_2011` is excluded | LGA dummies are included | Only Amangku uses LGA predictors. |

## 2. PBS variables

| Component | Ahn | Amangku |
|---|---|---|
| Lookback period | Seven years before rollout | Seven years before rollout |
| Item representation | ATC codes, especially ATC5 | Raw `itm_cd` codes |
| Item variables initially constructed | ATC5 quantity and dummy | Item frequency and quantity |
| Quantity used in final model | No; `pbs_atc5_quant_*` is excluded | No; `pbs_item_qty*` is deleted |
| Item variable used in final model | Selected `pbs_atc5_dummy_*` | `pbs_item_freq*`, converted to 0/1 ever-use indicators |
| Upstream item restriction | No comparable raw-item user-count rule identified | Keeps items used by more than 50 children |
| Final prevalence restriction | Keeps ATC5 dummies with prevalence at least `0.0005` among children with the selected NDIS outcome | No additional final-stage restriction |
| Clinical categories | Approximately 29 categories | None |
| Category × age variables | Ages 0–17 | None |
| `observe_age_*` variables | Ages 0–17 | None |
| Other PBS summaries | Visits, drug counts, repeat use, and ATC2 summaries | None identified |

See [PBS medication categories](pbs-classification.md) for the category definitions.

## 3. MBS variables

| Component | Ahn | Amangku |
|---|---|---|
| Lookback period | Seven years before rollout | Seven years before rollout |
| Collapse level | Child × item | Child × item |
| Upstream item restriction | At least 100 children | More than 100 children |
| Item variables initially constructed | Item dummy and item count | Item frequency |
| Item variable used in final model | Item dummy; item count is excluded | Item frequency is converted to a 0/1 ever-use indicator |
| Final prevalence restriction | Keeps item dummies with prevalence at least `0.0005` among children with the selected NDIS outcome | No additional final-stage restriction |
| Subgroup variables | Dummy and number of items | None |
| Specialty variables | Dummy, visits, and number of providers | None |

## 4. Merge and data preparation

| Step | Ahn | Amangku |
|---|---|---|
| AEDC | Merged and retained for prediction | Merged but excluded before prediction |
| MBS | Merges item, subgroup, and specialty blocks | Merges item block only |
| PBS | Merges ATC/category-based variables | Merges raw-item variables |
| Missing medical and DOMINO values | Filled with zero | Filled with zero |
| MBS after merge | Item counts excluded; item dummies retained | Nonzero item frequencies converted to one |
| PBS after merge | Quantities excluded; dummies and categories retained | Quantities deleted; nonzero item frequencies converted to one |
| DOMINO, ITR, and Payment Summary construction | Same | Same |
| Parent/spouse income missing values | Filled with zero | Filled with zero |
| Duplicate children before prediction | Keeps one row per `spine_id` after ordering by `main_caretaker` and `spouse_at_rollout` | No equivalent step appears in the final-model code; the upstream file may already be unique |
| Additional sample restrictions | Uses the constructed Ahn sample | Requires nonblank `parent_dom_synth` and excludes `pseudo_rollout == "2019-07-16"` |

## 5. Prediction training

| Component | Ahn | Amangku |
|---|---|---|
| Training sample | Random sample of 100,000 children | Full sample; `sample_frac(touse, 1)` only reorders the observations |
| Estimator | Binomial Lasso using `cv.glmnet` | Binomial Lasso using `cv.glmnet` |
| Cross-validation | 10 folds | 10 folds |
| CV metric | `type.measure = "mse"` | Not specified |
| Lambda grid | 50 values | 100 values |
| Parallel fitting | No | Yes, using 10 cores |
| Design matrix | Dense | Sparse |
| Lambda used for prediction | Explicitly `lambda.min` | Not specified in `predict()` |
| Prediction | Full sample in chunks of 1,000,000 | Full sample in one sparse matrix |

## Items to check

1. Does the final Ahn `xvar` contain `par_avg_inc`, `income_bucket_*`, or both?
2. Does the final Ahn `xvar` contain `age_rollout`, `age_round_*`, or both?
3. Is the Amangku dataset already unique by `spine_id` before prediction?
4. What `type.measure` and prediction lambda are used by Amangku's installed `glmnet` version when they are not specified?
