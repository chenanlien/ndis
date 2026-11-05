# NDIS Treatment Definition among DSP Recipients

## Goal

- Construct an NDIS treatment measure among DSP recipients.
- The measure should be based on the NDIS access process, not only on which model predicts NDIS entry best.
- Main variables to consider:
  - disability type;
  - impairment rating; and
  - work capacity.
- Two possible designs:
  - **Proposal A:** binary high- versus low-NDIS-likelihood groups;
  - **Proposal B:** an NDIS treatment-intensity measure.

## Current status and workflow

1. ~~Review the NDIS access process.~~ **Completed.**
2. **In progress:** confirm the original items under each condition group and check the coding of impairment rating and work capacity.
3. Complete the first-stage descriptive table and compare actual NDIS entry rates across these variables.
4. Construct Proposal A and Proposal B.
5. Compare their first-stage strength and sample sizes.
6. Repeat the checks for the Census `needs assistance = yes` sample.
7. Select the main design before looking at the outcome results.
8. Use LASSO as a final completeness check.

## NDIS access process: review completed

The rollout-period NDIS Act shows that a person first had to meet the age and residence requirements. The access request had to be made before age 65. The person also had to meet either the disability requirements or the early-intervention requirements.

For the disability pathway, the main requirements were:

- an intellectual, cognitive, neurological, sensory, physical, or psychiatric impairment;
- the impairment was, or was likely to be, permanent;
- substantially reduced functional capacity in at least one area:
  - communication;
  - social interaction;
  - learning;
  - mobility;
  - self-care; or
  - self-management;
- an effect on social or economic participation; and
- likely need for NDIS support for life.

The main implication is that **diagnosis alone was not the access rule**. The decision focused on the impairment caused by the condition and its effect on daily functioning. Evidence about the impairment and functional capacity was also important.

NDIS guidance includes condition lists that help with part of the decision:

- **List A:** conditions likely to meet the disability requirements, including moderate-to-profound intellectual disability and some severe neurological or physical conditions;
- **List B:** conditions likely to result in permanent impairment, but further evidence of functional impact may still be needed.

These lists support the idea that some intellectual and neurological conditions may have a clearer path into the NDIS. However, they do not support treating all people in a broad condition group in the same way. The empirical design should therefore combine condition type with severity information.

For the project, the rollout-period legislation should be the main source. Later operational guidance can help explain the process, but I will check that any condition list used in the classification also applied in the relevant period.

## Institutional motivation

### Disability type

Starting hypothesis:

- intellectual and neurological conditions may have a higher probability of NDIS entry;
- musculoskeletal and psychosocial conditions may have a lower probability of NDIS entry.

The institutional review gives some support to the first part of this hypothesis because some severe intellectual and neurological conditions appear on the NDIS condition lists. However, the NDIS rules do not directly rank these four broad groups. The ranking remains an empirical hypothesis and must be checked using actual entry rates.

Psychosocial disability is not excluded from the NDIS. A psychiatric impairment could meet the disability requirements if it was likely to be permanent and caused the required functional impact. A lower observed entry rate for this group, if found, should therefore be explained as an empirical pattern rather than a formal exclusion.

### Severity

- Disability type may not capture differences in severity.
- Impairment rating and work capacity may provide additional information.
- Expected pattern: more severe impairment or lower work capacity should be related to higher NDIS entry.
- I will not choose exact cutoffs now. They will be chosen after reviewing the variable coding and first-stage patterns.

Important distinction:

- impairment rating and work capacity are DSP measures of work-related impairment;
- they are not the formal NDIS functional-capacity test; and
- they should be used as available proxies for severity, then validated against actual NDIS entry.

## Sample

### Main sample

- DSP recipients observed before or around the NDIS rollout.
- Important caveat: DSP eligibility depends on both disability rules and income/assets tests.
- Therefore:
  - DSP receipt is not a pure measure of disability severity;
  - the sample is also selected by financial eligibility; and
  - results may not apply to all people with disability.

### Census-restricted sample

- DSP recipients with `needs assistance = yes` in the Census.
- This may give a narrower sample with greater functional limitations and stronger relevance to the NDIS.
- I will treat this as an alternative sample definition, not only a sensitivity check.
- However, Census `needs assistance` is not the same as NDIS eligibility.

## Proposal A: Binary treatment and control groups

- Create a **high-NDIS-likelihood group** and a **low-NDIS-likelihood group**.
- Use combinations of:
  - disability type;
  - impairment rating; and
  - work capacity.
- Initial idea:
  - intellectual/neurological conditions with higher severity may be in the high-likelihood group;
  - musculoskeletal/psychosocial conditions with lower severity may be in the low-likelihood group.
- This is not the final classification. It needs to be checked against actual NDIS entry.
- If some cases cannot be clearly classified, I can keep a middle group or leave them out of the main binary comparison.

**Main advantage:** easy to explain as treatment and control groups.

**Main concern:** a binary split may lose information and may depend too much on the selected cutoff.

## Proposal B: Treatment intensity

- Construct low, medium, and high NDIS-likelihood groups, or a continuous measure.
- Use the same institutional variables: disability type, impairment rating, and work capacity.
- This may be better if actual NDIS entry increases gradually rather than showing a clear high-low split.
- The measure should remain simple and interpretable. It should not be selected only because it gives the best prediction.

## First-stage validation

I am currently working on the first-stage descriptive table below. The first task is to confirm the original items included under each condition group and check the coding of impairment rating and work capacity.

| Panel A: Primary condition | N | NDIS entrants | NDIS entry rate |
|---|---:|---:|---:|
| Intellectual | ... | ... | ... |
| Neurological | ... | ... | ... |
| Psychosocial | ... | ... | ... |
| Musculoskeletal | ... | ... | ... |
| Other | ... | ... | ... |
| Missing/unknown | ... | ... | ... |

| Panel B: Impairment rating | N | NDIS entrants | NDIS entry rate |
|---|---:|---:|---:|
| <20 | ... | ... | ... |
| 20–29 | ... | ... | ... |
| 30+ | ... | ... | ... |
| Missing/unknown | ... | ... | ... |

| Panel C: Work capacity | N | NDIS entrants | NDIS entry rate |
|---|---:|---:|---:|
| 0–7 hours | ... | ... | ... |
| 8–14 hours | ... | ... | ... |
| 15+ hours | ... | ... | ... |
| Missing/unknown | ... | ... | ... |

The impairment-rating and work-capacity ranges are provisional. I will confirm them after reviewing the coding, the relevant DSP rules, and the number of observations in each category.

For the condition groups, I will document which original disability or diagnosis items are classified as intellectual, neurological, psychosocial, musculoskeletal, or other. This mapping needs to be fixed before comparing NDIS entry rates.

After completing the marginal table, I will also check:

- NDIS entry rates for condition-by-impairment combinations;
- NDIS entry rates for condition-by-work-capacity combinations;
- whether the high-likelihood group has a clearly higher entry rate than the low-likelihood group;
- whether entry increases with the proposed treatment intensity; and
- whether each group has enough observations.

I will also run simple regressions of NDIS entry on disability type and severity. The purpose is to check whether the proposed variables have first-stage relevance when included together.

## Census extension

I will repeat the first-stage analysis for the Census `needs assistance = yes` sample and compare it with the full DSP sample.

Questions to check:

- Does the high-low difference become larger?
- Does the intensity pattern become clearer?
- Is the ordering across disability groups similar?
- Is the restricted sample still large enough?

## Role of LASSO

- Use LASSO after the institution-based treatment measure is developed.
- Check whether LASSO also selects disability type, impairment rating, and work capacity.
- Check whether it identifies other strong predictors that may have been missed.
- Do not add a variable only because LASSO selects it. First check whether it has a clear institutional explanation and whether it could be a post-treatment variable.

## Sources for the access-process review

- [NDIS Act 2013, rollout-period compilation: sections 21–25](https://www.legislation.gov.au/C2013A00020/2014-07-01/2014-07-01/text/original/pdf)
- [NDIS disability requirements](https://www.ndis.gov.au/applying/eligibility-requirements/what-are-ndis-disability-requirements)
- [Applying to the NDIS appendices: List A and List B](https://www.ndis.gov.au/media/7752/download?attachment=)
- [DSP Impairment Tables](https://www.dss.gov.au/income-support-payments/disability-support-pension/disability-support-pension-impairment-tables)
