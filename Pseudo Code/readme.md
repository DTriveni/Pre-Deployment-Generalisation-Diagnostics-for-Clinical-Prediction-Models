# Data Exploration – eICU Demo

## Overview

This Python script explores the **eICU Collaborative Research Database Demo** to generate summary statistics that support the **fairness, privacy, and generalisation limitations** discussed in the `data_ethics.tex` section of the research portfolio. It does not build or evaluate models; it only produces descriptive output.

## Data Source

- **Name:** eICU Collaborative Research Database Demo (v2.0.1)
- **Host:** PhysioNet (https://physionet.org/content/eicu-crd-demo/2.0.1/)
- **Original publication:** Pollard, T. J., Johnson, A. E. W., Raffa, J. D., Celi, L. A., Mark, R. G., & Badawi, O. (2018). The eICU Collaborative Research Database, a freely available multi‑centre database for critical care research. *Scientific Data*, 5, 180178.
- **Description:** De‑identified multi‑centre intensive care unit (ICU) database containing 2,520 patients across 186 US hospitals. Includes demographics, APACHE physiology scores, lab results, diagnoses, and in‑hospital mortality.

## License

- **Demo subset:** Freely available without access restrictions under the **PhysioNet Open Data Commons Attribution License 1.0** (ODC‑By v1.0).
- **Full database (73,718 patients):** Requires CITI training and a signed data use agreement under the **PhysioNet Credentialed Health Data License v1.5.0** (restricted access).
- **Terms:** Research use only; no redistribution or commercial use. Users must cite the original publication and the PhysioNet platform.

> **Note:** This project uses only the demo subset. No identifiable patient information is accessed, and all analysis respects the license terms. The copy of the license has been attached to the folder. 

## What the code does

1. **Loads** the eICU patient, hospital, and (optionally) APACHE tables from a local folder.
2. **Prints demographic distributions** – ethnicity, gender, age – to show population skew (e.g., ~80% Caucasian, older age profile).
3. **Calculates mortality rates by US region** to demonstrate meaningful between‑context variation (e.g., Northeast vs. Midwest).
4. **Counts hospitals with very few patients** (<10) to highlight re‑identification risk under UK GDPR.
5. **Explicitly states limitations** – US‑only data and low ethnic diversity.

## Inputs

- **Data folder** – must contain at least `patient.csv` (or `.csv.gz`) and `hospital.csv`.
- The script assumes a specific `DATA_PATH` variable – **you must change this to your local path**.

## Outputs

- Console‑printed tables and counts:
  - Ethnicity percentages (Caucasian, African American, Hispanic, etc.)
  - Gender distribution (Male/Female)
  - Age summary (mean, median, min, max, valid count)
  - Region‑wise patient counts, hospital counts, mortality rates
  - List of hospitals with <10 patients (if any)
  - Statement about US‑only data and Caucasian proportion
  - Overall in‑hospital mortality percentage

No files are written; all output is to the terminal.

## Dependencies

- Python 3.7+
- pandas
- numpy

Install with:
```bash
pip install pandas numpy