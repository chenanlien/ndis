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
