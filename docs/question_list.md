# Question List
## Week 7/13/2026 - 7/16/2026

In 02-sample-construction.R
- line 83 - 132: discrad people whose ndis_rollout is NA or in 2021-2024 in raw data
  - 0.2M out of 4.1M (5%)
  - no obvious pattern, just simply because their location info is missing in raw data   
- line 200: from `ndis_accessrequests` get NDIS-kids `ndis_start` date
  - 7061 out of 1212536 (0.5%) people have duplicate record
  - now we basically keep each first record of these people

## Week 7/17/2026 - 7/22/2026

In 03-sample-parent-match.R
- line 45 - 51: discard people have no dom_synth (ID in DOMINO)
- no detail under check

In 04-sample-aedc.R
- Amangku claimed there is a preliminary prediction model but I don't see it. 
- this code is straight forward no much questions
