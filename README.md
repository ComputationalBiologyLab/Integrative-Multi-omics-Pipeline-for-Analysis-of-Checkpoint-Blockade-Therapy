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

## Key Contributions

- Identification of **482 response-associated features** using multi-method feature selection  
- Predictive modeling achieving **89% accuracy (SVM)** for ICB response classification  
- Construction of **gene regulatory networks (GRNs)** and **co-mutational networks**  
- Discovery of **11 candidate biomarkers** with prognostic relevance:  
  - **Expression-associated:** `EEA1`, `RFX5`, `SALL2`, `TBX2`  
  - **Mutation-associated:** `DLGAP2`, `INSRR`, `MBD5`, `PIK3C2G`, `RYR2`, `SCN1A`, `SLITRK3`  

---

## Data Sources

This study integrates two publicly available multi-omics immunotherapy cohorts:

- **Liu et al. (2019)** – Metastatic melanoma  
- **Ravi et al. (2023)** – Non-small cell lung cancer (NSCLC)  

Final merged dataset:

- **184 patients** with matched multi-omics profiles  
- Response defined using **RECIST criteria** (CR/PR vs SD/PD)  

---

## Data Modalities

The pipeline incorporates:

1. **Clinical Data**  
   - Demographics, treatment, survival outcomes, tumor characteristics  

2. **Transcriptomics**  
   - RNA-seq (TPM normalized)  

3. **Somatic Mutations (SNPs)**  
   - Aggregated mutation counts per gene  

4. **Copy Number Alterations (CNA)**  
   - Discrete (Liu) and discretized continuous values (Ravi)  

5. **Immune Cell Composition**  
   - Estimated using **CIBERSORTx (LM22 signature matrix)**  

---

## Pipeline Architecture

### 1. Data Preprocessing

- Harmonization of clinical variables across datasets  
- Gene ID standardization (**HUGO → Entrez**)  
- Batch correction using **limma (`removeBatchEffects`)**  
- Filtering to **protein-coding genes (Ensembl BioMart)**  
- Min-max normalization of numerical features  

---

### 2. Feature Selection

Four independent methods:

- Random Forest importance  
- Recursive Feature Elimination (RFE)  
- ANOVA F-statistic  
- Mutual Information  

Final feature set:

- **Union of selected features → 482 features**

---

### 3. Machine Learning Models

- Support Vector Machine (**best performance**)  
- Random Forest  
- Logistic Regression  

Evaluation strategy:

- Stratified **80/20 train-test split**  
- Hyperparameter tuning via **grid search**  

---

### 4. Network Analysis

#### Gene Regulatory Networks (GRNs)

- Constructed using **GENIE3**  
- Separate networks for responders and non-responders  
- Hub definitions:
  - **In-degree:** regulated genes  
  - **Out-degree:** transcription factors  

#### Co-mutational Networks

- Based on mutation co-occurrence across patients  
- Edge weight reflects co-mutation frequency  
- Node size = degree, color = mutation burden  

---

### 5. Survival Analysis

- Elastic Net Cox model (**scikit-survival**)  

Performance:

- Expression features: **C-index = 0.76**  
- SNP features: **C-index = 0.88**  

---

## Repository Structure

```

IMPACT/
│
├── Code/
│   ├── MAIN_CODE.ipynb
│   └── R Codes/
│       ├── Batch correction.R
│       ├── GRN.R
│       └── mapping.R
│
├── Data/
│   ├── Liu/
│   ├── Ravi/
│   └── Merged/

```

### Notes

- Core pipeline implemented in **Python (Jupyter Notebook)**  
- Supporting analyses implemented in **R**:
  - Batch correction  
  - Gene regulatory networks  
  - Gene ID mapping  

---

## Important Notes

- Immune cell abundances are **not provided in raw datasets**  
  → computed using **CIBERSORTx**

- Co-mutational networks represent:
  - **co-occurrence structure**, not differential mutation testing  

- Feature selection uses **union strategy (primary analysis)**  

---

## Data Availability

- Liu dataset: available via **cBioPortal**  
- Ravi dataset: available via **Zenodo**  

Refer to the manuscript for access details and usage policies.

---

## Code Availability

GitHub repository:

https://github.com/ComputationalBiologyLab/Predicting-Immune-Checkpoint-Blockade-Response-A-Multi-Omics-Based-Machine-Learning-Framework

---

## Citation

If you use this pipeline or processed data, cite:

**Saadawy et al. – Integrative Multi-omics Pipeline for Analysis of Checkpoint Blockade Therapy**

---

## License

This repository is provided for reproducibility.  
Users must comply with licensing terms of the original datasets.
```
