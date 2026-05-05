# Pre-Deployment Generalisation Diagnostics for Clinical Prediction Models

A framework for predicting and preventing performance collapse when clinical ML models are applied to new patient populations.

---

## Student Information

- **Name:** Triveni Dhamdhere
- **Student ID:** lo25538
- **Module:** SEMTM0045 — Data Science Projects and Methods
- **Theme:** Theme 3
- **Topic:** Topic 3 — Construction of Robust Predictive Models for Reliable Generalisation

---

## Research Summary

Clinical prediction models frequently fail when deployed on patients from different hospitals, demographics, or time periods. This project proposes a three-stage pre-deployment diagnostic framework:

1. **Divergence Scoring** — Measures how different the target population is from training data using MMD, KL divergence, and Wasserstein distance
2. **Feature Stability Analysis** — Identifies which predictors are reliable across contexts and which are not
3. **Decision Framework** — Maps scores to actionable recommendations: deploy, adjust, restrict, or withhold

Proof-of-concept tested on the eICU Collaborative Research Database Demo (2,520 ICU patients across 186 US hospitals).

---

## Repository Structure

SEMTM0045_Theme3_Topic3_tdhamdhere/
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
│       ├── eicu_framework_results.png
│       └── gantt_chart.png
│
├── code/
│   ├── eicu_framework.py
│   ├── methodology.md
│   └── requirements.txt
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

---

## Data

This project uses the **eICU Collaborative Research Database Demo v2.0.1**.

The data is freely available from PhysioNet:
https://physionet.org/content/eicu-crd-demo/2.0/

Due to PhysioNet licence terms, data files are **not included** in this repository. To reproduce the analysis:

1. Visit the link above
2. Download the demo dataset
3. Place the CSV files in the `data/` directory
4. Run `python code/eicu_framework.py`

---

## Key Findings

- **KL divergence** significantly predicted cross-region performance variation (Spearman r=0.58, p=0.048), outperforming MMD (r=0.43) and Wasserstein distance (r=0.22)
- **Feature stability analysis** identified ventilation status, motor GCS, hematocrit, temperature, and age as shift-stable predictors; FiO2, respiratory rate, and creatinine as shift-sensitive
- **Decision framework** correctly flagged the highest-risk deployment scenarios while clearing low-divergence pairs for deployment
- Cross-region performance (0.686) did not collapse like Chekroud et al.'s psychiatric models, suggesting ICU mortality prediction using standardised physiology variables is more transportable

---

## Tools Used

- **Language:** Python 3.10+
- **Libraries:** scikit-learn, pandas, numpy, scipy, matplotlib
- **Document Preparation:** LaTeX (IEEEtran class)
- **Project Management:** Trello
- **Version Control:** Git / GitHub

---

## How to Run

```bash
# Install dependencies
pip install -r code/requirements.txt

# Run the framework (requires eICU demo data in data/ folder)
python code/eicu_framework.py
```

---

## How to Compile the Paper

```bash
cd latex_source
pdflatex main.tex
bibtex main
pdflatex main.tex
pdflatex main.tex
```

---

## Project Management

- **Actual project board:** [Insert Trello link]
- **Hypothetical 12-week plan:** [Insert Trello link]

---

## References

Full reference list available in `latex_source/references.bib` (36 sources in IEEE format).

Key foundation papers:
- Chekroud et al. (2024) — Illusory generalizability of clinical prediction models. *Science*
- Whalen et al. (2022) — Navigating the pitfalls of applying ML in genomics. *Nature Reviews Genetics*
- Pavlovic et al. (2024) — Improving generalization using causal modelling. *Nature Machine Intelligence*
- Collins et al. (2024) — TRIPOD+AI statement. *BMJ*
- Wörheide et al. (2021) — Preventing dataset shift from breaking ML biomarkers. *GigaScience*

---

## Licence

This repository contains academic coursework. The eICU data is governed by the PhysioNet Credentialed Health Data Use Agreement and is not redistributed here.