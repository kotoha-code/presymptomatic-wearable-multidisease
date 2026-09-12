# Pre-symptomatic Detection of Chronic Diseases from Wearable, Survey, and Clinical Data

Multimodal machine learning for pre-symptomatic detection of **type 2 diabetes (T2D)**,
**schizophrenia (SCZ)**, **depression (DEPR)**, and **coronary artery disease (CAD)**
from wearable, survey, and clinical data in the
[*All of Us* Research Program](https://allofus.nih.gov/) (CDR v9, Controlled Tier).

## Overview

Chronic diseases may leave behavioral and physiological traces before diagnosis.
This project asks whether wearable, survey, and clinical data from a strictly
**pre-symptomatic window (548–180 days, i.e. 18–6 months before diagnosis)** can
predict disease onset, which modalities contribute, and how this differs across
diseases.

**Research questions**
1. Can pre-symptomatic wearable/survey/clinical data predict onset?
2. Which modalities contribute, and how does this differ across diseases?
3. Do sleep- and circadian-rhythm features add value beyond standard summaries?

## Key findings

- **Wearable data alone** (6–18 months before diagnosis) predicts onset at
  moderate accuracy (AUROC 0.66–0.72), **without diagnosis-proxy labs**.
- **Multimodal integration** improves prediction in every disease
  (e.g. T2D reaches AUROC 0.72 with wearable + survey; HbA1c raises it to 0.78
  but is a diagnosis proxy and is treated as exploratory).
- **Disease-specific feature importance**: metabolic/cardiac diseases are driven
  by BMI and activity volume; psychiatric diseases by sleep and activity *quality*.
  Statistical screening, SHAP, and logistic coefficients agree.
- **"Irregularity" is a cross-disease signal**: sleep-duration SD is significant
  in all four diseases; circadian rhythm variability (RA_sd) adds independent,
  disease-associated information (adopted for SCZ, where it improves prediction).
- **Model choice depends on n**: TabPFN and tree models are the ceiling for this
  tabular, mid-sized data; a simple MLP only matches logistic regression.
  For SCZ (n=101) results are exploratory; cross-validation is essential.

## Pipeline

| File | Step |
|------|------|
| `01_cohort_matching.py` | Matched case-control cohort (4:1 on sex, age ±5y, calendar time) |
| `02_feature_extraction.py` | Window-based wearable / survey / lab features |
| `03_circadian_features.py` | Circadian rhythm features (M10/L5/RA, cosinor) from minute-level steps |
| `04_statistical_tests.py` | Univariate screening (Mann-Whitney U, rank-biserial, Bonferroni) |
| `05_machine_learning.py` | Modality ablation, 4 models, stratified 5-fold CV, AUROC/AUPRC |
| `06_shap_analysis.py` | SHAP feature-contribution analysis (tree + kernel explainers) |

## Study design

- **Cohort**: for each disease, cases (diagnosed, with wearable data in the
  window) are matched 4:1 to disease-negative Fitbit users on sex (exact) and
  age (±5 years). The case's diagnosis date is borrowed as the control's index
  date, aligning calendar time. BMI is a predictor, not a matching variable.
- **Leakage control**: imputation, scaling, and one-hot encoding are fit within
  each CV fold; age is integer-normalized.
- **Metrics**: AUROC and AUPRC (baseline = prevalence ≈ 0.24).

## Requirements

- Access to the *All of Us* Controlled Tier via the Researcher Workbench.
- Python packages in `requirements.txt`.
- Genomic components (polygenic scores) are selected but not computed here
  (v9-scale computation was infeasible with the available tooling); see the
  paper's future-work section.

## Data & ethics

This repository contains **code only**. No participant-level data, identifiers,
or derived files are included. All analyses were run inside the *All of Us*
Controlled Tier in compliance with the program's Data Use Agreement. Data paths
and identifiers are read at runtime from the researcher's own workspace.

Following *All of Us* policy, no AI/AN-specific analyses are performed; all
analyses are conducted as general cross-ancestry analyses.

## Citation

If you use this code, please cite this repository and the *All of Us* Research
Program. (Add paper citation here once available.)

## Acknowledgments

Developed during a summer research exchange at the Computational Wellbeing Group,
Rice University. Sleep and circadian feature-extraction logic adapted from the
group's reference implementation.
