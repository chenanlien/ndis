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
| Parent income | `par_avg_inc` enters linearly; no income buckets | `par_avg_inc` is converted into `income_bucket_*` dummies | Linear income versus income-category dummies. |
| Child age | `age_at_rollout` enters linearly | Rounded and converted into `age_round_*` dummies | Linear age versus age-category dummies. |
| Location | `lga_code_2011` is excluded | LGA dummies are included | Only Amangku uses LGA predictors. |
| Number of predictors | 6,143 | 3,429 | Fill in the Amangku predictor count. |

## 2. PBS variables

| Component | Ahn | Amangku |
|---|---|---|
| Lookback period | Seven years before rollout | Seven years before rollout |
| Item representation | Raw PBS items are mapped to ATC5 and aggregated at the child × ATC5 level; no raw-item predictors are used | Raw `itm_cd` codes |
| Reason for item representation | Follows the adult-sample prediction setup | Uses Amangku's raw-item setup |
| Item variables initially constructed | ATC5 quantity and dummy | Raw-item frequency and quantity |
| Quantity used in final model | No; `pbs_atc5_quant_*` is excluded | No; `pbs_item_qty*` is deleted |
| Item variable used in final model | Selected `pbs_atc5_dummy_*`; no raw-item predictors | `pbs_item_freq*`, with every nonzero frequency converted to a 0/1 ever-use indicator |
| Upstream item restriction | Keeps items used by more than 50 children | Keeps items used by more than 50 children |
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
| MBS after merge | Item counts excluded; item dummies retained | All nonzero values across the MBS item-frequency columns are converted to one |
| PBS after merge | Quantities excluded; ATC dummies and categories retained; no raw-item predictors | Quantity columns are deleted, and all nonzero values across the PBS item-frequency columns are converted to one |
| DOMINO, ITR, and Payment Summary construction | Same | Same |
| Parent/spouse income missing values | Filled with zero | Filled with zero |

## Items to check

1. How many predictors are used in the Amangku model?
