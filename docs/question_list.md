# Question List
## Week 7/13/2026 - 7/16/2026

In 02-sample-construction.R
- line 83 - 132: discrad people whose ndis_rollout is NA or in 2021-2024 in raw data
  - 0.2M out of 4.1M (5%)
  - no obvious pattern, just simply because their location info is missing in raw data   
- line 200: from `ndis_accessrequests` get NDIS-kids `ndis_start` date
  - 7061 out of 1212536 (0.5%) people have duplicate record
  - now we basically keep each first record of these people
- 3.9 million kids after this step 

## Week 7/17/2026 - 7/22/2026

In 03-sample-parent-match.R
- line 45 - 51: discard 132,951 kids having no dom_synth (ID in DOMINO)
  - 3.9 million - 0.13 million = 3.7 million 
- 0.8% kids have no parent, :(
- Some kids have duplicate parental records (35%)
  - we currently pick the parent record closest to rollout date
  - then use the spouse record to identify the parent's spouse at rollout (if valid)
  
#### Example timeline

Suppose child `A` is linked to two possible parents, `B` and `C`.

##### Parent–child relationships

| Child | Parent | Relationship start date |
|---|---|---:|
| A | B | 2017-11-03 |
| A | C | 2010-02-14 |

##### Spouse relationships

| Person 1 | Person 2 | Relationship start date | Relationship end date |
|---|---|---:|---:|
| B | C | 2008-04-02 | 2016-08-04 |
| B | D | 2016-09-03 | Ongoing |

The implied timeline is:

```text
2008-04-02          2010-02-14          2016-08-04   2016-09-03   2017-11-03
    |                    |                   |            |            |
    B–C starts           A–C starts          B–C ends     B–D starts   A–B starts
```
This example shows that the recorded parent–child relationship dates do not necessarily coincide with the biological or original parental relationship. In particular, A–B begins after the B–C relationship has ended and after the B–D relationship has begun.

In 04-sample-aedc.R
- Amangku claimed there is a preliminary prediction model but I don't see it.
  - there is no preliminary prediction model (checked with Tobias)
- this code is straight forward

## Week 7/23/2026 - 7/31/2026

In 07, 08, 09 R scripts
- They mainly collect and collapse kids' MBS/PBS data and parents' DOMINO data
- We need to decide how many years of records before rollout should be used in the prediction model
  - one version uses records from 5 years before rollout
  - another version uses records from 7 years before rollout
- After reviewing the code, the difference is only the length of the lookback window
  
| Year | PBS | MBS | DOMINO |
|---|---|---|---|
| 5 | 3,053,269 | 3,656,456 | 5,611,954 |
| 7 | 3,132,151 | 3,707,483 | 5,611,954 |

In 12, 13 R scripts
- We need to decide which parent's data should be used in the prediction model
- baseline (Amangku's version): using main_caregiver and their latest spouse(if exists)
- the def of main_caregiver: the parent record closest to rollout date

## Week 8/01/2026 - 8/06/2026

In 07-sample-pbs-collapsed.R
- We labelled drugs into 29 categories
- You can find the full list from [pbs-classification.md](https://github.com/chenanlien/ndis/blob/main/docs/pbs-classification.md)
- Issue about age_interaction:
  - We could construct age-specific medical variables before collapsing the records, producing 29 medical categories × 18 ages. 
  - However, each child is observed only during the seven years before rollout, so the observable age range differs by age at rollout. 
  - Therefore, a zero in a category-by-age variable may indicate either no medical record at that age or that the child was not observed at that age.

## Week 8/07/2026 - 8/12/2026

### Kids Prediction Model: Current Status

- **Sample size:** ~3.7 million children.
- Before running the prediction model, I dropped **199 children with missing gender information**.
- I currently include about **8,000 candidate predictors**, most of which come from **MBS and PBS records**.
- But only 78 predictors have non-zero coefficient.
- In the adult prediction model, there is an additional screening step based on the prevalence of individual MBS/PBS items before they enter the final model. I have **not implemented this screening yet**, but I plan to follow the same approach.
- The current dataset is computationally demanding. Even using only **10,000 children for model training**, running the full prediction pipeline takes about **6 hours**. My next priority is therefore to improve computational efficiency.
### Main Differences from the Adult Prediction Model

| Adult model | Current kids model |
|---|---|
| Training sample is first restricted using `dsp_sample` | The kids sample itself is the prediction population |
| Uses SA2/SA3 location controls | I currently exclude location variables |
| MBS/PBS items are further screened based on prevalence | I have not yet implemented this screening |
| Uses a manually constructed variable list (`M18vars`) | I currently include almost all usable predictors after excluding IDs, outcomes, post-NDIS variables, and location variables |
| Uses adult-specific age, ATO, Domino, MCD, and Census variables | Kids model uses child characteristics, AEDC, parent/spouse Domino, parent income, MBS, and PBS variables |
| Includes adult-specific interactions | Kids model additionally includes age-specific PBS category variables |
| Training uses a large share of the adult prediction sample | Because the kids sample is much larger, I currently need to use a smaller training subsample for computational feasibility |

## Week 8/14/2026 - 8/18/2026

### Prediction Model Comparison

The first table compares different model specifications. The updated main-caregiver model performs best overall, with lower MSE and higher target coverage across all reported cutoffs.

| Metric | Model - Main Caregiver | Model - Non Main Caregiver | Amangku Model |
|---|---:|---:|---:|
| MSE              | **0.0454** | 0.0466 | 0.0512 |
| Top 1% capture   | **12.73%** | 12.51% | 12.61% |
| Top 5% capture   | **39.56%** | 37.73% | 34.98% |
| Top 10% capture  | **53.35%** | 51.61% | 46.19% |
| Top 20% capture  | **69.67%** | 68.41% | 58.92% |
| Top 30% capture  | **79.55%** | 78.62% | 68.21% |

### Different Training Size

The second table holds the model specification fixed and compares the effect of increasing the training sample from 100k to 200k observations.

| Metric | Size - 100k | Size - 200k |
|---|---:|---:|
| MSE              | 0.0458 | **0.0455** |
| Top 1% capture   | **12.97%** | 12.92% |
| Top 5% capture   | **39.76%** | 39.64% |
| Top 10% capture  | 53.02% | **53.83%** |
| Top 20% capture  | 70.31% | **71.01%** |
| Top 30% capture  | 80.54% | **80.87%** |

Predictions from the 100k and 200k models are highly correlated (`r = 0.9865`). Increasing the training sample to 200k slightly improves MSE and capture rates at broader targeting thresholds, but overall predictions are very similar.

> Note: The 100k model in the training-size comparison is not directly identical to the 100k model reported above. The training-size exercise uses a fixed holdout evaluation sample and draws the training observations from the remaining sample, so both the training set and evaluation set differ from the main model comparison.
