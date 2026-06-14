
# Pre-Deployment & Post-Deployment Diagnostic Framework for Generalisation Failure in Clinical Prediction Models

## Overview

Clinical machine learning models frequently fail to generalise beyond the populations they were trained on. This project proposes and implements a practical three-stage diagnostic pipeline that enables clinical ML teams to assess generalisation risk **before** a model is deployed — without requiring specialist expertise in causal inference.

The framework addresses a well-documented but underserved problem: while the field has strong tools to explain why models fail *after the fact*, it has few tools to predict these failures *beforehand*. This pipeline fills that gap.

---

## The Problem

Models trained on one hospital's data often fail on another hospital's patients. A landmark 2024 study in *Science* (Chekroud et al.) found that schizophrenia treatment models dropped from an AUC of 0.72 in internal testing to 0.54 on independent datasets — approaching coin-flip performance. Similar failures have been reported in genomics, immunology, and biomarker research.

Root causes include:
- **Dataset shift** — training and deployment populations differ
- **Spurious correlations** — models learn associations that don't hold across contexts
- **Confounding** — hidden variables inflate apparent model performance
- **Data leakage** — test data contaminates training pipelines

---

## The Three-Stage Pipeline

### Stage 1 — Divergence Scoring
*How different is the new patient group from the training group?*

Three distributional distance metrics are computed in parallel:

| Metric | Strength |
|---|---|
| Maximum Mean Discrepancy (MMD) | Distribution-free; no shape assumptions |
| KL Divergence | Sensitive to unseen patient types in the target |
| Wasserstein Distance | Robust when training and target have little overlap |

Each metric is correlated (Spearman) against observed performance drops across known deployment contexts to identify which metric best predicts real-world failure.

### Stage 2 — Feature Stability
*Which features can be trusted across different patient groups?*

A leave-one-context-out permutation importance method scores each predictor for consistency across subgroups (hospitals, regions, time periods):

| Stability Label | Consistency Score |
|---|---|
| Shift-stable | > 0.6 |
| Uncertain | 0.3 – 0.6 |
| Shift-sensitive | < 0.3 |

This replicates the intent of causal graph-based feature selection (Pavlovic et al., 2024) without requiring a manually constructed causal diagram.

### Stage 3 — Deployment Decision Framework
*What should the practitioner do?*

Divergence score and stability profile feed into a four-outcome decision matrix:

| Divergence | Feature Stability | Recommendation |
|---|---|---|
| Low | — | Deploy; quarterly monitoring |
| Moderate | ≥ 70% stable | Deploy; monthly monitoring |
| Moderate | 50–70% stable | Retrain on stable features only |
| Moderate / High | < 50% stable | Restrict or do not deploy |
| High | — | Do not deploy |

**Post-deployment:** Divergence is recomputed on incoming data at regular intervals. If it crosses the upper threshold, the model is flagged for human review. If balanced accuracy falls below 0.60, the model is paused automatically.

---

## Repository Structure
```
SEMTM0045_Theme3_Topic3_lo25538/
│
├── README.md
├── SEMTM0045_Theme3_Topic3_tdhamdhere.pdf
│
├── latex_source/
│   ├── main.tex
│   ├── IEEEtran.cls
│   ├── references.bib
│   ├── sections/
│   │   ├── introduction.tex
│   │   ├── literature_review.tex
│   │   ├── methodology.tex
│   │   ├── results.tex
│   │   └── discussion.tex
│   └── figures/
│       ├── pipeline_overview.png
│       ├── decision_flowchart.png
│       └── gantt_chart.png
│       
│
├── code/
│   ├── data_loading.ipynb
│   └── license.txt
│   
│
├── planning/
│   ├── gantt_chart.pdf
│   ├── project_timeline.pdf
│   └── project_management_links.txt
│
├── data/
│   └── README.md  (data access instructions — data not included due to licence)
│
└── research_evidence/
├── literature_notes/
│   ├── chekroud2024_summary.md
│   ├── whalen2022_summary.md
│   ├── pavlovic2024_summary.md
│   ├── springer2021_summary.md
│   └── collins2024_summary.md
├── paper_matrix.xlsx
├── research_gap_analysis.pdf
└── methodology_sketch.pdf
```
---

---

## Data

### Primary Dataset
**eICU Collaborative Research Database Demo** (Pollard et al., 2018)  
Hosted on [PhysioNet](https://physionet.org/content/eicu-crd-demo/)

- 2,520 ICU patients across 186 US hospitals
- Variables: demographics, APACHE physiology scores, lab results, diagnoses, in-hospital mortality
- Multi-hospital structure provides the cross-context variation the framework requires

The demo subset is freely downloadable. The full database (73,718 patients) requires CITI training and a signed Data Use Agreement.

### Data Grouping
Due to small per-hospital patient counts in the demo subset (10–40 per hospital), hospitals are grouped into four US regions for analysis:

| Region | Approx. Patients |
|---|---|
| Midwest | ~807 |
| South | ~500 |
| West | ~300 |
| Northeast | ~159 |

### Data Notice

> All data used in this project is sourced from the **eICU Collaborative Research Database Demo**, made available by PhysioNet under their data use licence. Data is used strictly for **educational and research purposes** as part of the MSc Data Science and AI programme at the University of Bristol. Data must not be redistributed or used for commercial purposes. See the [PhysioNet licence](https://physionet.org/content/eicu-crd-demo/view-license/) for full terms.

---

## Baselines

The framework is evaluated against three baselines:

| Baseline | Description |
|---|---|
| Naive cross-validation | Internal CV performance treated as deployment performance — the current default |
| Importance weighting | Best single correction method from the literature (Wörheide et al.) |
| Random feature subsets | Models retrained on randomly selected feature subsets of equal size to the stable set |

---

## Evaluation Metrics

**Primary metric:** Spearman correlation between predicted performance drop (Stage 1 divergence) and observed performance drop on external data.

**Secondary metrics:**
- Balanced accuracy (matching Chekroud et al.'s primary measure)
- Calibration slope (per TRIPOD+AI requirements)
- Balanced accuracy stratified by demographic subgroup
- Concordance rate between predicted and observed feature instability (target: ≥ 70%)

---

## Ethical Considerations

| Risk | Mitigation |
|---|---|
| Unfair thresholds (80% Caucasian calibration population) | All results reported stratified by demographic subgroup; calibration population limitations stated explicitly |
| Re-identification risk in small subgroups | No subgroup statistics computed below n = 10; divergence computed on aggregate distributions only |
| Automation bias (categorical recommendations mistaken for certainties) | Divergence score and uncertainty range displayed alongside recommendation; human sign-off required before any deployment action |
| Feature exclusion inequity | Demographic impact checked before dropping shift-sensitive features; targeted recalibration used if removal disproportionately harms specific groups |
| Governance | PhysioNet data use terms followed in full; DUAA 2025 human-review and auditability requirements apply in any UK clinical deployment |

---

## Tools and Libraries

| Tool | Purpose |
|---|---|
| Python 3.x | Core pipeline implementation |
| pandas, numpy | Data preparation and manipulation |
| scikit-learn | Permutation importance, model evaluation |
| scipy | KL divergence, Wasserstein distance |
| POT (Python Optimal Transport) | MMD and Wasserstein distance computation |
| YAIB framework | Standardised ICU data handling and baseline modelling |

---

## Key References

- Chekroud et al. (2024). Illusory generalizability of clinical prediction models. *Science*, 383(6679), 164–167.
- Whalen et al. (2022). Navigating the pitfalls of applying machine learning in genomics. *Nature Reviews Genetics*, 23(3), 169–181.
- Pavlovic et al. (2024). Improving generalisation of ML-identified biomarkers using causal modelling. *Nature Machine Intelligence*.
- Gretton et al. (2012). A kernel two-sample test. *JMLR*, 13, 723–773.
- Collins et al. (2024). TRIPOD+AI statement. *BMJ*, e078378.
- Ben-David et al. (2010). A theory of learning from different domains. *Machine Learning*, 79(1–2), 151–175.
- Pollard et al. (2018). The eICU Collaborative Research Database. *Scientific Data*, 5, 180178.

---

## Licence

This repository is submitted as coursework for the University of Bristol MSc Data Science and AI programme and is intended for academic use only. Data from the eICU Collaborative Research Database Demo is used under the PhysioNet licence; redistribution or commercial use is not permitted.

## Licence

This repository contains academic coursework. The eICU data is governed by the PhysioNet Credentialed Health Data Use Agreement and is not redistributed here.
