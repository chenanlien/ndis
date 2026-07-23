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
- this code is straight forward

## Week 7/23/2026 - 7/31/2026
- summary stats about child-parent linkage:
  -  table of parent before_rollout
  
| before_rollout | n | percent |
|---|---:|---:|
| FALSE | 487771 | 9% |
| TRUE | 4815961 | 91% |
| All | 5303732 | 100% |

  - table of parental linkage

| n_children | mean_num_parent | mean_before_rollout | mean_after_rollout | 
|---:|---:|---:|---:|
| 3,795,375 | 1.4 | 1.3 | 0.1 |
