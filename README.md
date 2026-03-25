# IMPACT

Repository for the publication: **Integrative Multiomics Pipeline for Analysis of Checkpoint Therapy**

## Overview

IMPACT is a comprehensive bioinformatics pipeline designed to integrate and analyze multi-omics data from immunotherapy cohorts. This repository contains code and datasets from two major immunotherapy studies (Liu, Ravi), along with processing scripts for data standardization and cross-study integration.

## Project Structure

### `Code/` Directory
Contains all analysis and data processing scripts:

- **`MAIN_CODE.ipynb`** - Primary Jupyter notebook containing the main analysis pipeline, integrating all data processing and modeling steps
- **`R Codes/`** - Supporting R scripts for specialized analyses:
  - `Batch correction.R` - Batch effect correction across datasets
  - `GRN.R` - Gene Regulatory Network analysis
  - `mapping.R` - Gene ID mapping and annotation utilities

### `Data/` Directory
Organized by study and processing stage:

#### **`Liu/`** - Liu et al. immunotherapy cohort
Raw and processed multi-omics data:
- **Clinical Data**: `data_clinical_patient.tsv`, `data_clinical_sample.tsv`
- **Gene Expression**: `data_mrna_seq_tpm.txt`, `processed_exp_data_liu.tsv`
- **Copy Number Alterations (CNA)**: `data_cna.txt`, `processed_cna_data_liu.tsv`
- **Mutations**: `data_mutations.txt`, `processed_snp_data_liu.tsv`
- **Immune Cell Profiling**: `CIBERSORTx.csv` (immune cell deconvolution)
- **Mapping Files**: Gene ID mapping for CNA (`mapping_liu_cna.tsv`), expression (`mapping_liu_exp.tsv`), and SNP (`mapping_liu_snp.tsv`)
- **Biomart Annotation**: `mart_export.txt` (gene annotations)
- **Clinical Labels**: `labels_liu.tsv`

#### **`Ravi/`** - Ravi et al. immunotherapy cohort
Raw and processed multi-omics data with tumor purity information:
- **Clinical Data**: `clinical.xlsx`, `processed_clinical_data_ravi.tsv`
- **Gene Expression**: `mrna_tpm.tsv`, `processed_exp_data_ravi.tsv`
- **Copy Number Alterations**: `gistic_gene.tsv`, `processed_cna_data_ravi.tsv`
- **Mutations**: `Absolute_MAF_Final.tsv`, `processed_snp_data_ravi.tsv`
- **Tumor Purity**: `purity_ploidy.tsv`, `total_amps_dels.tsv`
- **Tumor Mutational Burden**: `tmb.tsv`
- **Antigen Presentation**: `antigen_presentation.tsv`
- **Mapping Files**: Gene ID mapping for CNA, expression, and SNP data
- **Clinical Labels**: `labels_ravi.tsv`

#### **`Merged/`** - Integrated multi-study dataset
Cross-study integrated and harmonized data:
- **Clinical Data**: `merged_clinical.tsv`
- **Gene Expression**: `merged_expression.tsv` (harmonized across studies)
- **Copy Number Alterations**: `merged_cna.tsv`
- **SNP/Mutations**: `merged_snp.tsv`, `merged_snp_pre_normalization.tsv`
- **Immune Cell Profiling**: `cibersort_merged.csv`
- **Mapped Data**: Pre-processed datasets with standardized gene symbols:
  - `mapped_merged_exp.tsv`
  - `mapped_merged_cna.tsv`
  - `mapped_merged_snp.tsv`
- **Mapping Files**: Gene ID mapping tables for CNA, expression, and SNP data
- **Merged Labels**: `merged_labels.tsv` (unified response/outcome labels)

## Data Types

The pipeline integrates the following multi-omics data types:

1. **Gene Expression** - mRNA abundance quantification (TPM/RPKM normalized)
2. **Copy Number Alterations** - Somatic chromosomal gains/losses
3. **Somatic Mutations** - SNVs and Indels from whole-exome/whole-genome sequencing
4. **Clinical Data** - Patient demographics, treatment history, response to immunotherapy
5. **Immune Cell Composition** - Tumor-infiltrating immune cell estimates (CIBERSORT deconvolution)

## Key Features

- **Multi-study Integration**: Harmonizes data across two independent immunotherapy cohorts
- **Batch Correction**: Removes technical batch effects across different sequencing platforms and studies
- **Gene Regulatory Network Analysis**: Identifies key regulatory relationships in immunotherapy response
- **Co-Mutational Network Analysis**: Identifies candidate genes that are significantly co-mutated or have increased mutational rates between the two groups.
- **Feature Selection**: Identifies relevant genes for predicting ICB success by integrating four different feature selection methods.
- **Machine Learning**: Assesses the predictive capacity of the identified features on the task of predicting ICB response.
- **Survival Analysis**: Identifies candidate genes relevant for ICB response prediction (Intersection between Gene Regulatory Networks and Co-Mutational Networks) and performs survival analysis on them.


## Data Availability

Please refer to the original publications for access information and data usage policies.

## Citation

If you use this pipeline or processed data in your research, please cite the original publications.

## License

This repository is maintained for reproducibility of the publication. Please check individual data sources for licensing terms.
