# Ahn vs Amangku Prediction Models

## Purpose and evidence standard

This note compares the Ahn and Amangku child NDIS prediction pipelines. The comparison is intended to identify which modeling choices could explain differences in prediction rankings and downstream event-study pre-trends.

This note is based on the Ahn and Amangku source-code extracts reviewed during the model-comparison exercise. Each entry is labelled as follows:

我想要取消status label，不要再講confirmed, verified這些label, too complicated
- **Confirmed**: directly shown by the reviewed code. 
- **User-confirmed**: reported to be identical across pipelines, but not re-checked line by line in the comparison materials.
- **Verify**: the available code is incomplete or relies on an implicit software default.

## Executive summary

The models are not the same Lasso applied to slightly different covariates. They differ in the prediction target, medical-history representation, final feature screening, treatment of age and income, location controls, and training sample. The highest-priority candidates for explaining different prediction rankings are:

1. `ndis_dum` versus `ndis_2year` as the target;
2. Ahn's richer PBS and MBS feature architecture versus Amangku's raw-item ever-use indicators;
3. Ahn's 100,000-child training subsample versus Amangku's full-sample training;
4. exclusion versus inclusion of LGA indicators; and
5. continuous/general covariate use versus bucketed income and age dummies.

## 1. Overall prediction variables

| Dimension | Ahn | Amangku | Status and implication |
|---|---|---|---|
| Prediction target | `ndis_dum` | The reviewed model sets `y2` to `ndis_2year`; it also constructs `ndis_ever` and `ndis_enter_kid` | **Confirmed; high importance.** The fitted outcomes differ. 這個很難講，我們已經試過both ndis_ever and ndis_2year for both two models，這裡至少可以說pred_outcome 有對齊|
| AEDC | Retained; the reviewed preparation code fills relevant numeric missings and includes an AEDC match indicator | Variables beginning `aedc_` are removed before model fitting | **Confirmed; potentially substantive.** |
| MBS | Selected item dummies plus subgroup and specialty measures | Raw MBS item variables, converted to ever-used indicators before fitting | **Confirmed; high importance.** See Section 3. |
| PBS | ATC-based variables, clinical categories, category-by-age measures, observation-age indicators, and selected ATC5 dummies | Raw PBS item variables, converted to ever-used indicators before fitting | **Confirmed; high importance.** See Section 2. |
| Parent DOMINO | Included | Included via `par_dom_*` | **Confirmed.** Upstream construction was reported identical. |
Parent/spouse income 與 Child age 這裡再展開說說，讓我確定具體是差在哪？給我例子，讓我理解。此外你怎麼發現amangku是用 income bucket，我以為我也有？
| Parent/spouse income | Upstream three-year household measures, including `par_avg_inc`, remain available to the exclusion-based predictor set | `par_avg_inc` is converted to 100,000-unit income buckets and then dummy-coded | **Confirmed; potentially substantive.** Verify the exact final Ahn income columns retained. |
| Child age | `age_rollout` appears to remain as an ordinary predictor | `age_rollout` is rounded and dummy-coded as `age_round_*` | **Ahn: verify exact retained column; Amangku: confirmed.** The functional form differs if Ahn retains the continuous measure. |
| Location | `lga_code_2011` is explicitly excluded | `lga_code_2011` is dummy-coded and included | **Confirmed; high importance.** |

## 2. PBS variable details

| Component | Ahn | Amangku | Status and implication |
|---|---|---|---|
| Pre-rollout window | Seven years | Seven years | **Confirmed.** |
| Identifier/representation | Claims are linked to the PBS drug map and represented through ATC levels, especially ATC5, plus constructed clinical categories | Raw `itm_cd` item codes | **Confirmed; substantive.** |
| Item-level measures initially constructed | ATC5 quantity and dummy measures | `pbs_item_freq` and `pbs_item_qty` by child × item | **Confirmed.** |
| Quantity in final model | `pbs_atc5_quant_*` variables are excluded | `pbs_item_qty*` variables are deleted during merge/preparation | **Confirmed.** Neither final specification uses the constructed quantity block. |
| Item variable used in final model | Selected `pbs_atc5_dummy_*` indicators | Variables still named `pbs_item_freq*`, but converted to `0/1` before fitting | **Confirmed.** Amangku's final `freq` names do not contain frequencies. |
| Upstream rarity rule | No comparable raw-item user-count threshold was identified in the reviewed Ahn PBS construction | Keeps raw items used by more than 50 children (`pbs_item_total > 50`) | **Confirmed for reviewed code.** Do not describe this as a 2% rule; the executable condition is an absolute count. |
| Final prevalence screen | Keeps ATC5 dummies with prevalence at least `0.0005` among children with `ndis_dum == 1` | No additional final-model prevalence filter was shown | **Confirmed.** Ahn uses an outcome-case prevalence screen; Amangku's filtering occurs upstream. |
| Clinical categories | Approximately 29 medication/treatment indicators | Not shown | **Confirmed in Ahn; no corresponding Amangku block identified.** See [PBS medication categories](pbs-classification.md). |
| Category × age | Category-specific indicators for ages 0–17 | Not shown | **Confirmed in Ahn.** |
| Observation-age controls | `observe_age_*` for ages 0–17 | Not shown | **Confirmed in Ahn.** These distinguish unobserved ages from observed ages with no claim. |
| Other summaries | Includes measures such as visits, drug counts, repeat use, and ATC2 summaries | Final model is centered on raw-item ever-use indicators | **Confirmed for the reviewed feature-construction code; verify the exact Ahn summaries surviving the final exclusion list.** |

In short, the PBS histories are represented at different levels:

```text
Ahn:     ATC hierarchy + clinical categories + category × age
         + observation-age controls + screened ATC5 dummies

Amangku: raw PBS item ever used (0/1)
```

The key difference is therefore not merely the rarity threshold; it is the representation of prescription history.

## 3. MBS variable details

| Component | Ahn | Amangku | Status and implication |
|---|---|---|---|
| Pre-rollout window | Seven years | Seven years | **Confirmed.** |
| Collapse level | Child × MBS item | Child × MBS item | **Confirmed.** |
| Upstream item rule | Keeps items observed for at least 100 children | Keeps items observed for more than 100 children | **Confirmed.** The strictness cannot be inferred from these thresholds alone because Ahn applies another screen later. |
| Item measures constructed | Item dummy and item count | Item frequency | **Confirmed.** |
| Item measure entering final model | Item dummy; `mbs_item_count_*` is removed | `mbs_item_freq*` is converted to an ever-used `0/1` indicator | **Confirmed.** Both ultimately use item-level ever-use indicators, but from differently screened item sets. |
| Final prevalence screen | Keeps item dummies with prevalence at least `0.0005` among children with `ndis_ever` | No additional final-model prevalence filter was shown | **Confirmed.** |
| Subgroup features | `mbs_sub_dummy_*` and `mbs_sub_items_*` | No corresponding block shown | **Confirmed.** |
| Specialty features | Specialty dummy, visit count, and provider count | No corresponding block shown | **Confirmed.** |

Ahn therefore applies a two-stage item-selection process: an upstream child-count threshold followed by a prevalence threshold among NDIS cases. Amangku applies the upstream child-count restriction but no final-stage prevalence screen in the reviewed model script.

## 4. Merge and data-preparation differences

| Step | Ahn | Amangku | Status and implication |
|---|---|---|---|
| AEDC | Left-merged, with `aedc_match` constructed; AEDC variables remain available for prediction | Left-merged upstream, but `aedc_` variables are later removed from the model data | **Confirmed.** |
| MBS merge | Item block plus separate subgroup/specialty block | Item block only | **Confirmed; substantive.** |
| PBS merge | Ahn ATC/category feature architecture | Amangku raw-item feature architecture | **Confirmed; substantive.** |
| Missing medical/DOMINO features | Relevant missing values are filled with zero | Relevant missing values are filled with zero | **Confirmed.** |
| MBS transformation after merge | Item counts are excluded at final selection; constructed item dummies are used | All nonzero `mbs_item_freq*` values are converted to one | **Confirmed.** |
| PBS transformation after merge | Quantity variables are excluded at final selection; constructed dummies/categories are used | `pbs_item_qty*` is deleted and all nonzero `pbs_item_freq*` values are converted to one | **Confirmed.** |
| DOMINO, ITR, and Payment Summary construction | Same reported construction | Same reported construction | **User-confirmed.** Treat these as common upstream components unless a later line-by-line audit finds a difference. |
| Parent/spouse income missing values | Filled with zero before household totals/average are constructed | Same reported handling | **Confirmed in reviewed merge code; common logic reported by user.** Final model functional form still differs. |
| Duplicate child handling | Before fitting, rows are ordered by descending `main_caretaker` and `spouse_at_rollout`, then one row per `spine_id` is retained | No equivalent step was visible in the reviewed final-model code | **Verify.** Amangku's upstream data may already be one row per child; compare duplicate counts immediately before fitting. |
| Final sample restrictions | The reviewed final code uses its constructed Ahn sample | Requires nonblank `parent_dom_synth` and excludes `pseudo_rollout == "2019-07-16"` | **Amangku confirmed; verify the directly comparable Ahn restrictions.** |

The merge architecture is broadly similar, but the feature blocks merged and the transformations applied after merge are not. The largest differences arise from MBS/PBS construction and AEDC retention, rather than from DOMINO or income-source construction.

## 5. Prediction training differences

| Training feature | Ahn | Amangku | Status and implication |
|---|---|---|---|
| Target | `ndis_2year` | `ndis_2year` 一樣的問題，我們試過兩個變數在兩個model | **Confirmed; high importance.** |
| Training observations | Random sample of 100,000 children | `sample_frac(touse, 1)`: 100% of observations, randomly reordered | **Confirmed; high importance.** `1` means 100%, not 1%. |
| Estimator | `cv.glmnet`, binomial Lasso | `cv.glmnet`, binomial Lasso | **Confirmed.** Default `alpha = 1` should be recorded explicitly in future production code. |
| Cross-validation | 10 folds | 10 folds | **Confirmed.** |
| CV metric | `type.measure = "mse"` | Not specified | **Confirmed as written; verify effective `glmnet` default for the installed version.** |
| Lambda grid | `nlambda = 50` | `nlambda = 100` | **Confirmed; potentially substantive.** It may change the selected penalty. |
| Parallel execution | `parallel = FALSE` | `parallel = TRUE` with 10 registered cores | **Confirmed; mostly implementation.** |
| Design matrix | Dense matrix | Sparse matrix | **Confirmed; mostly implementation.** |
| Iteration control | Reviewed code uses the Ahn fit settings | `maxit = 10000` is passed through `cv.glmnet` | **Confirmed as written.** Check package warnings because argument handling can vary by version. |
| Lambda used for prediction | Explicit `s = "lambda.min"` | `s` is not explicit in the reviewed `predict()` call | **Confirmed as written; verify effective default.** This should be made explicit before a controlled comparison. |
| Prediction universe | Full sample, processed in chunks of 1,000,000 | The same full sparse sample used for fitting is scored directly | **Confirmed.** Chunking versus one-pass scoring is implementation only. |
| In-sample scoring | Training is on 100,000, followed by full-sample scoring | Fits and predicts on the same full sample | **Confirmed; substantive for calibration and overfit diagnostics.** |

## Confirmed differences versus open checks

### Confirmed high-priority differences

- Prediction target: `ndis_dum` versus `ndis_2year`.
- PBS representation: ATC/category/age architecture versus raw-item ever-use.
- MBS representation: Ahn subgroup/specialty blocks and two-stage screening versus Amangku raw-item ever-use with upstream screening only.
- Location: excluded versus included as LGA dummies.
- Training sample: 100,000 versus the full sample.
- AEDC: retained versus removed.
- Income and age functional forms.

### Confirmed implementation differences

- Dense versus sparse matrices.
- Parallel versus non-parallel fitting.
- Prediction in chunks versus one pass.
- Reshape batches of 50 versus 25 items.

These implementation choices should not be treated as explanations for score differences without evidence of convergence, precision, or data-loss problems.

### Items requiring verification

1. Export the realized Ahn and Amangku predictor names immediately before matrix construction and compare them directly.
2. Confirm exactly which Ahn income and age columns survive the final exclusion list.
3. Count duplicate `spine_id` values immediately before each model fit and determine whether Ahn's deduplication changes the sample.
4. Make Amangku's CV folds reproducible by recording the seed and supplying/storing `foldid`.
5. Record the installed `glmnet` version and make `type.measure`, `alpha`, and prediction `s` explicit in both scripts.
6. Record the final retained MBS/PBS feature counts after every screen.
7. Compare the exact model matrices—not only IDs, outcomes, and selected demographics—when reproducing an earlier score file.

## Recommended controlled comparison

To isolate whether the prediction specification drives the downstream pre-trends, first hold the sample, prediction target, event-study code, score cutoffs, outcomes, fixed effects, and clustering constant. On the successfully replicated Amangku sample, change only the prediction specification:

```text
Amangku sample + Amangku prediction specification
Amangku sample + Ahn prediction specification
```

For each pair, report:

- prediction correlation and absolute score differences;
- score distributions;
- treatment/control group sizes and overlap;
- capture and precision at the chosen cutoffs; and
- the same event-study plots and pre-trend tests.

After that baseline comparison, replace one component at a time—target, PBS block, MBS block, AEDC, income form, age form, location, training size, and CV settings. This sequence separates substantive feature differences from implementation details.
