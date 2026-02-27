# PPMI Visit-Level Data Integration + Visualization

This repository contains two Jupyter notebooks that:
1) integrate multiple **objective assessment** tables into a single **visit-level** dataset, and  
2) perform **quality checks & visualization** to validate distributions and labels for downstream ML.

---

## Notebooks

### 1) `Clinical Data preparation_normalized-Objective_all_Visit.ipynb`
**Objective non-motor data integration (PPMI)**:
- Loads multiple objective assessment CSV files
- Creates a common visit key: **`PATNO_EVENT = PATNO + EVENT_ID`**
- Cleans each table (drops missing rows, removes duplicates, keeps last)
- Merges datasets (inner join on `PATNO_EVENT`)
- Exports:
  - **Raw merged dataset (not normalized)**
  - **Z-score normalized dataset** (numeric columns only)

**Inputs (expected paths)**
- `data/Assessed(Objective)_MDS-UPDRS_Part_III_28Sep2025.csv`
- `data/Benton_Judgement_of_Line_Orientation_29Sep2025.csv`
- `data/Hopkins_Verbal_Learning_Test_-_Revised_29Sep2025.csv`
- `data/Letter_-_Number_Sequencing_29Sep2025.csv`
- `data/Modified_Semantic_Fluency_29Sep2025.csv`
- `data/Montreal_Cognitive_Assessment__MoCA__29Sep2025.csv`
- `data/Symbol_Digit_Modalities_Test_29Sep2025.csv`

**Outputs**
- `reports/Objectives_Features_All_Visit_not_normalized.csv`
- `reports/Objectives_Features_All_Visit_normalized_zscore.csv`

---

### 2) `Data Visualization for classification.ipynb`
**Dataset validation for classification**:
- Loads prepared subjective + objective feature tables (visit-level)
- Inspects `NHY` distribution and removes invalid/out-of-scope values (e.g., `NHY = 101.0`)
- Creates a 3-class label:
  - **Healthy:** `NHY = 0`
  - **Mild-PD:** `NHY = 1–2`
  - **Severe-PD:** `NHY = 3–5`
- Produces plots for:
  - class balance
  - missingness / QC checks
  - before/after filtering distributions (as implemented in the notebook)

**Inputs (expected paths)**
- `data/Subjectives_Features_All_Visit_not_normalized_nan_removed.csv`
- `data/Oubjectives_Features_All_Visit_not_normalized_SFT1_nan_removed.csv`

---

## Recommended folder structure

```
.
├─ data/                      # raw inputs (NOT committed if sensitive)
├─ reports/                   # exported merged tables
├─ notebooks/
│  ├─ Clinical Data preparation_normalized-Objective_all_Visit.ipynb
│  └─ Data Visualization for classification.ipynb
├─ requirement.yml            # conda environment file
└─ README.md
```

> If your data is not public, add it to `.gitignore` (recommended).

---

## Setup (Conda)

Create the environment:

```bash
conda env create -f requirement.yml
conda activate ppmi-data-integration
```

Run Jupyter:

```bash
jupyter lab
```

Open the notebooks from the `notebooks/` folder and run top-to-bottom.

---

## Notes
- The main join key is **`PATNO_EVENT`**; keep it consistent across all tables.
- Normalization uses **z-score** on numeric columns only; ID columns remain unchanged.
