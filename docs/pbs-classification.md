## PBS Medication Categories

`pbs_category` contains 29 binary indicators showing whether a child had at least one PBS record in each medication or treatment category during the pre-rollout window.

These variables are based on PBS medication classifications and should be interpreted as **medication-based health proxies**.

### Mental health

1. `pbs_mental` — Broad mental health medication category
2. `pbs_antipsychotic` — Antipsychotic medications
3. `pbs_anxiolytic` — Anxiolytic medications
4. `pbs_hypnotic` — Hypnotic and sedative medications
5. `pbs_antidepressant` — Antidepressant medications
6. `pbs_adhd` — ADHD-related medications

The following variables are subsets of `pbs_mental`:

- `pbs_antipsychotic`
- `pbs_anxiolytic`
- `pbs_hypnotic`
- `pbs_antidepressant`
- `pbs_adhd`

### Pain and substance-use treatment

7. `pbs_opioid` — Opioid analgesics
8. `pbs_addiction_treatment` — Medications used in addiction treatment
9. `pbs_pain` — Broad pain-related medication category

`pbs_opioid` is generally a subset of the broader `pbs_pain` category.

### Cancer and immune-related treatment

10. `pbs_cancer` — Broad cancer-treatment category
11. `pbs_endocrine` — Endocrine therapy
12. `pbs_immunostimulant` — Immunostimulant therapy
13. `pbs_immunosuppressant` — Immunosuppressant therapy
14. `pbs_medical_oncology` — Medical oncology treatment
15. `pbs_herceptin` — Herceptin-related treatment

These variables may overlap with `pbs_cancer`, but they identify more specific treatment types rather than mutually exclusive disease groups.

### Cardiovascular conditions

16. `pbs_cardiovascular` — Broad cardiovascular medication category
17. `pbs_antihypertensive` — Antihypertensive medications

`pbs_antihypertensive` is kept separate from `pbs_cardiovascular` to match the adult-sample classification.

### Respiratory and infectious conditions

18. `pbs_asthma` — Asthma and obstructive airway disease medications
19. `pbs_hepatitis_c` — Hepatitis C treatment

### Metabolic, musculoskeletal, and genitourinary conditions

20. `pbs_diabetes` — Diabetes-related medications
21. `pbs_bone` — Bone and osteoporosis-related medications
22. `pbs_gout` — Gout medications
23. `pbs_bladder` — Bladder and urinary-condition medications
24. `pbs_osteoarthritis` — Osteoarthritis-related medications
25. `pbs_fatigue` — Fatigue- or stimulant-related medications

Some medications may appear in more than one of these categories. For example, parts of the osteoarthritis classification may also overlap with the broader pain category.

### Treatment-program and administrative categories

26. `pbs_palliative` — Palliative-care treatment
27. `pbs_general_schedule` — General PBS schedule category
28. `pbs_highly_specialised` — Highly specialised drug program
29. `pbs_pq` — PBS-specific program or administrative category

The final four groups are primarily treatment-program or PBS administrative classifications and should not be interpreted as individual diseases.

### Summary of nested categories

```text
pbs_mental
├── pbs_antipsychotic
├── pbs_anxiolytic
├── pbs_hypnotic
├── pbs_antidepressant
└── pbs_adhd

pbs_pain
└── pbs_opioid

pbs_cancer
├── pbs_endocrine
├── pbs_immunostimulant
├── pbs_immunosuppressant
├── pbs_medical_oncology
└── pbs_herceptin
