# Data Source Documentation

## Data availability and lookback period

The following table summarizes the currently identified data sources, available years, and the construction of variables based on the existing code.

| Data source | Available years identified from code | Lookback period | Variable construction / usage |
|---|---|---|---|
| PBS | 2007–2020 | 7 years before rollout | Prescription records are extracted and classified into disease/drug categories following the adult sample approach. | 
| MBS | 2007–2019 | 7 years before rollout | Medical service claims are extracted and grouped based on MBS categories/subgroups following the adult sample approach. |
| ATO ITR | FY2006–07 to FY2022–23 | three pre-rollout income measures | Extract `incm_or_lss_totl_amt` from ITR data. Income variables are constructed for the three fiscal years before rollout. | 
| ATO Payment Summary | FY2006–07 to FY2023–24 | used as fallback | Used when ITR income is missing or zero. |
| DOMINO | 2006–2021 | 7 years before rollout | Creates parent/spouse DOMINO history indicators. Household-level indicators combine parent and spouse information. |

---

## ATO income construction

| Variable | Description |
|---|---|
| `parent_inc_1` | Parent income in rollout year - 2 |
| `parent_inc_2` | Parent income in rollout year - 3 |
| `parent_inc_3` | Parent income in rollout year - 4 |
| `par_sps_inc_1` | Spouse income in rollout year - 2 |
| `par_sps_inc_2` | Spouse income in rollout year - 3 |
| `par_sps_inc_3` | Spouse income in rollout year - 4 |
| `par_total_1` | Parent + spouse income (year 1) |
| `par_total_2` | Parent + spouse income (year 2) |
| `par_total_3` | Parent + spouse income (year 3) |
| `par_avg_inc` | Average household income over the three pre-rollout years |

Notes:
- Income is based on total income/loss reported in ATO records (`incm_or_lss_totl_amt`).
- Need to confirm whether this measure only captures employment income or includes other income sources.

---

## DOMINO construction

| Variable group | Description |
|---|---|
| `par_dom_*` | Parent DOMINO indicators |
| `sps_dom_*` | Spouse DOMINO indicators |
| `com_dom_*` | Combined household DOMINO indicators |

Household-level variables are constructed as:
**combined indicator = max(parent indicator, spouse indicator)**
Therefore, the household indicator equals 1 if either parent or spouse has the corresponding DOMINO characteristic.
