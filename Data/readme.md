# Data

## Primary Dataset

**eICU Collaborative Research Database Demo v2.0.1**

- **Source:** PhysioNet
- **URL:** https://physionet.org/content/eicu-crd-demo/2.0/
- **Licence:** PhysioNet Credentialed Health Data Use Agreement (research use only, no redistribution, no commercial use)
- **Citation:** Pollard, T.J. et al. (2018). The eICU Collaborative Research Database. Scientific Data, 5, 180178.

## Why This Dataset

The eICU demo contains 2,520 ICU patients across 186 US hospitals with demographics, APACHE physiology variables, lab results, and mortality outcomes. The multi-hospital structure provides natural deployment contexts for testing whether prediction models generalise across independent clinical environments — the core question this project addresses.

## Data Not Included

The data files are **not included** in this repository because the PhysioNet licence prohibits redistribution. This is not a Creative Commons licence — sharing the files, even in a private repository, would violate the data use agreement.

## How to Get the Data

1. Go to https://physionet.org/content/eicu-crd-demo/2.0/
2. The demo subset is freely available — no credentialing required
3. Download the following files:
   - `patient.csv.gz`
   - `apacheApsVar.csv.gz`
   - `apachePatientResult.csv.gz`
   - `hospital.csv.gz`
   - `lab.csv.gz` (optional, not used in core pipeline)
   - `diagnosis.csv.gz` (optional, not used in core pipeline)
4. Place the downloaded files in this `data/` directory
5. Run the pipeline: `python code/eicu_framework.py`

## Files Used in This Project

| File | Contents | Role in Pipeline |
|------|----------|-----------------|
| `patient.csv.gz` | Demographics, hospital ID, admission details, discharge status | Central table — mortality outcome and context variable (hospital/region) |
| `apacheApsVar.csv.gz` | APACHE physiology scores — heart rate, blood pressure, creatinine, GCS, temperature, etc. | Predictor features for mortality model |
| `apachePatientResult.csv.gz` | APACHE predicted mortality, actual mortality, diagnosis | Clinical baseline comparison and outcome labels |
| `hospital.csv.gz` | Hospital region, bed count, teaching status | Context metadata — hospitals grouped into four US regions |

## Preprocessing Steps

The following steps are applied in `code/eicu_framework.py`:

1. Join `patient` + `apacheApsVar` on `patientunitstayid`
2. Join with `hospital` on `hospitalid` to get region labels
3. Join with `apachePatientResult` (deduplicated) for APACHE baseline scores
4. Drop rows with no region assignment
5. Create binary mortality target from `hospitaldischargestatus`
6. Recode age: `> 89` converted to `90` as numeric
7. Impute missing physiology values using median imputation
8. Group hospitals into four regional contexts: Midwest, South, West, Northeast

## Limitations

- All hospitals are US-based — no international generalisability testing possible
- Demo subset has only 10–40 patients per hospital (median 12), requiring regional aggregation
- Patient population is 80% Caucasian, predominantly older and male
- The full eICU database (73,718 patients, 208 hospitals) would allow hospital-level analysis but requires PhysioNet credentialing

## Full Database Access

To access the complete eICU database:

1. Complete the free CITI "Data or Specimens Only Research" training course
2. Create a PhysioNet account
3. Submit a credentialed access application
4. Sign the PhysioNet Credentialed Health Data Use Agreement
5. Access at: https://physionet.org/content/eicu-crd/2.0/