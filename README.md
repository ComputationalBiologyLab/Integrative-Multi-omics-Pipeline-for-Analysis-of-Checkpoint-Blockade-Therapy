# IMPACT  
**Integrative Multi-omics Pipeline for Analysis of Checkpoint Blockade Therapy**

---

## Overview

IMPACT is an integrative and interpretable bioinformatics pipeline designed to identify biomarkers associated with response to immune checkpoint blockade (ICB) therapy across cancer types. The framework combines multi-omics data with machine learning and network-based analyses to characterize molecular determinants of treatment response.

The pipeline integrates **clinical, transcriptomic, genomic (SNP and CNA), and immune-related data** from independent immunotherapy cohorts and applies feature selection, predictive modeling, and systems-level analyses to identify clinically relevant biomarkers.

## Graphical Abstract

<p align="center">
  <img src="https://github.com/user-attachments/assets/fefa6ac4-a2e3-44ea-a0a6-019a40bcb694" width="800"/>
</p>

---

## Key contributions

- Identification of **482 response-associated features** using multi-method feature selection (union of selected features retained in the integrative matrix).
- **Nested cross-validation** (outer evaluation, inner grid search) for ICB response models, with reporting of **accuracy, F1, precision, recall, sensitivity, specificity, AUC-ROC, and AUPR** (see [Machine learning findings](#machine-learning-findings-full-482-feature-set)).
- **Strongest ranking performance** on the full feature set: **SVM AUC-ROC = 0.958 ± 0.028** and **AUPR = 0.944 ± 0.038**; **highest mean accuracy** on the same protocol: **logistic regression 0.886 ± 0.032**.
- Construction of **gene regulatory networks (GRNs)** and **co-mutational networks**.
- Discovery of **11 candidate biomarkers** with prognostic relevance:  
  - **Expression-associated:** `EEA1`, `RFX5`, `SALL2`, `TBX2`  
  - **Mutation-associated:** `DLGAP2`, `INSRR`, `MBD5`, `PIK3C2G`, `RYR2`, `SCN1A`, `SLITRK3`  

---

## Data sources

This study integrates two publicly available multi-omics immunotherapy cohorts:

- **Liu et al. (2019)** – metastatic melanoma (`Data/Liu/`)
- **Ravi et al. (2023)** – non-small cell lung cancer (NSCLC) (`Data/Ravi/`)

Merged cohort (processed tables under `Data/Merged/`):

- **184 patients** with matched multi-omics profiles in the merged expression / SNP / CNA tables  
- **ICB response** is encoded in `Data/Merged/merged_labels.tsv` and in the modeling table `Data/final_dataset.csv` using **RECIST-style** response (responders vs non-responders).  

---

## Data modalities

1. **Clinical** – demographics, treatment, tumor characteristics (processed merged clinical: `Data/Merged/merged_clinical.tsv`; cohort-specific processed files under `Data/Liu/` and `Data/Ravi/`).
2. **Transcriptomics** – TPM-level expression (`merged_expression.tsv`, `final_dataset.csv` gene columns).
3. **Single nucleotide polymorphisms (SNPs)** – gene-level binary SNP features (`merged_snp.tsv`, `merged_snp_pre_normalization.tsv`).
4. **Copy number alterations (CNA)** – discrete / discretized CNA features (`merged_cna.tsv`).
5. **Immune composition** – **CIBERSORTx** (LM22); merged file `Data/Merged/cibersort_merged.csv`.

Supporting reference files include **protein-coding gene lists** (`Data/protein_coding_genes.tsv`) and **Entrez ↔ symbol mapping** tables under `Data/Merged/` and cohort subfolders.

---

## Pipeline architecture

### 1. Data preprocessing

- Harmonization of clinical variables across cohorts.  
- Gene ID standardization (**HUGO ↔ Entrez**) using mapping tables in `Data/*/mapping_*.tsv` and R helpers in `Code/R Codes/mapping.R`.  
- Batch correction with **limma** (`removeBatchEffects`); script: `Code/R Codes/Batch correction.R`.  
- Filtering to **protein-coding genes** (`Data/protein_coding_genes.tsv`).  
- Min–max scaling where applied in the ML evaluation routine (fit on each outer-fold **training** split only).  

### 2. Feature selection

Four complementary approaches (as implemented in `MAIN_CODE.ipynb`): random forest importance, RFE, ANOVA *F*-test, and mutual information. The **union** of selected features defines the integrative panel (**482 features** in `Data/final_dataset.csv`, excluding `Sample Identifier` and `ICB Response`).

### 3. Machine learning models

- **Random Forest**, **SVM**, and **logistic regression** classifiers for binary ICB response.  
- **Nested cross-validation** in `MAIN_CODE.ipynb` (`nested_cv_evaluate`):  
  - **Outer loop:** stratified *k*-fold (*k* = 5, shuffled, `random_state=42`) for unbiased performance estimates.  
  - **Inner loop:** `GridSearchCV` with 5-fold CV on the **outer training** fold only; inner tuning optimizes **accuracy**.  
  - **MinMaxScaler** is fit on each outer training fold and applied to train/test for that fold (no leakage into held-out folds).  
- **`final_dataset.csv`** is the primary input for the “full feature set” evaluation block (same feature definitions as the integrative model).  

### 4. Network analysis

#### Gene regulatory networks (GRNs)

- Built with **GENIE3** (`Code/R Codes/GRN.R`).  
- Separate networks for responders and non-responders; hub interpretation via in-degree / out-degree.  

#### Co-mutational networks

- Mutation **co-occurrence** across patients; edge weights reflect co-mutation frequency.  

### 5. Survival analysis

- **Elastic-net Cox** models using **scikit-survival** (`CoxnetSurvivalAnalysis`), in `Code/Survival_analysis.ipynb`.  
- Uses expression and SNP feature sets **aligned with `Data/final_dataset.csv`**, merged with survival endpoints from Liu clinical tables and optional Ravi `clinical.xlsx` under `Data/Ravi/`.  
- Generates cross-validated concordance profiles, coefficient plots, and exports such as `transcriptomic_data_survival.csv` and `SNP_survival.csv`.

---

## Repository structure

```text
IMPACT/
│
├── README.md
├── Data/
│   ├── final_dataset.csv          # integrative modeling matrix (482 features + labels)
│   ├── protein_coding_genes.tsv
│   ├── Liu/                       # Liu cohort raw + processed tables, labels, CIBERSORTx
│   ├── Ravi/                      # Ravi cohort processed omics, labels, ancillary TSVs
│   └── Merged/                    # merged_expression, merged_snp(_pre_normalization), merged_cna,
│                                  # merged_clinical, merged_labels, cibersort_merged.csv, mappings
│
└── Code/
    ├── MAIN_CODE.ipynb            # full pipeline, nested CV evaluation, figures/tables
    ├── Survival_analysis.ipynb    # Cox elastic net survival models (scikit-survival)
    └── R Codes/
        ├── Batch correction.R
        ├── GRN.R
        └── mapping.R
```

### Notes

- Core predictive pipeline: **Python (Jupyter)** — `MAIN_CODE.ipynb`.  
- Supporting analyses: **R** — batch correction, GRNs, ID mapping.  

---

## Important notes

- Immune cell abundances are **not** in the raw downloads; they are estimated with **CIBERSORTx**.  
- Co-mutational networks summarize **co-occurrence**, not formal differential mutation testing.  
- Primary feature strategy for the integrative classifier is the **union** of feature-selection outputs.  

---

## Data availability

- **Liu** dataset: **cBioPortal** (see original study / portal accession).  
- **Ravi** dataset: **Zenodo** (see original publication).  

Refer to the manuscript for access details and usage policies.

---

## Code availability

Primary development / integrative pipeline repository:

https://github.com/ComputationalBiologyLab/Integrative-Multi-omics-Pipeline-for-Analysis-of-Checkpoint-Blockade-Therapy

---

## Citation

If you use this pipeline or processed data, cite:

**Saadawy et al. – Integrative Multi-omics Pipeline for Analysis of Checkpoint Blockade Therapy**

---

## License

This repository is provided for reproducibility. Users must comply with licensing terms of the original datasets.
