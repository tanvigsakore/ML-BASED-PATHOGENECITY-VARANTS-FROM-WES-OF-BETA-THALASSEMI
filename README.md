# ML-Based Pathogenicity Screening of CRISPR Off-Target Variants from Whole-Exome Sequencing of Beta-Thalassemia HSPCs

**Author:** Tanvi Sakore | **Supervisor:** Dr. Nilofer Shaikh | Biotecnika Info Labs Pvt. Ltd.

## Overview

CRISPR-based gene editing of hematopoietic stem and progenitor cells (HSPCs) is now a clinical reality for beta-thalassemia, but off-target editing — particularly splice-altering variants that standard deleteriousness scores tend to underestimate — remains an open safety concern. This project builds a reproducible, interpretable computational pipeline to detect and prioritize pathogenic off-target variants from whole-exome sequencing (WES) data of CRISPR-edited and control HSPC samples.

- **Data source:** 8 WES samples (4 CRISPR-edited, 4 unedited controls; 3 donors), publicly deposited at NCBI BioProjects [PRJNA1144227](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA1144227) and [PRJNA1144228](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA1144228)
- **Phase 1 (Galaxy):** Variant calling and functional annotation narrowed >9 million annotated variants down to 22 high-priority candidate off-target variants
- **Phase 2 (Python/ML):** An interpretable, single-feature (CADD Phred score) pathogenicity classifier — implemented with both Random Forest and XGBoost — trained on 80,692 curated ClinVar variants and validated via five-fold stratified cross-validation
- **Model performance:** Cross-validated AUC of 0.905 (Random Forest) and 0.906 (XGBoost); SHAP confirmed CADD Phred as the consistent driver of predictions
- **Key finding:** Applying the tuned models to the 22 Phase 1 candidates produced a consensus pathogenicity ranking, flagging six moderate-risk variants — with two SPICE1 splice-donor variants ranked highest
- **Critical validation step:** Targeted SpliceAI scoring revealed that the TNRC18 splice-donor variant scored near-maximal for splice disruption despite being classified low-risk by the CADD-based models — underscoring the need for multi-tool cross-validation rather than relying on a single annotation source

## Repository Structure

| Notebook | Purpose |
|---|---|
| `Phase2_Step1_Germline_Filter_v2.ipynb` | Two-step variant filtering (genotype presence in edited samples; population allele frequency) with control-presence feature engineering |
| `Phase2_Step2_v5_BatchCADD.ipynb` | Batch retrieval of real CADD Phred scores via the MyVariant.info API, with checkpointing for large-scale queries |
| `Phase2_Step3_Enrich_Variants_v2.ipynb` | Feature completion for the ~1.03M candidate variants (imputed CADD, SIFT, PolyPhen-2, GERP, SpliceAI via impact-stratified simulation) |
| `Phase2_Step4_ML_Model_v3.ipynb` | Random Forest pathogenicity classifier (300 trees, single CADD feature), five-fold cross-validation |
| `Phase2_Step5_SMOTE_XGBoost_v3.ipynb` | XGBoost classifier with SMOTE resampling, hyperparameter tuning via GridSearchCV |
| `Phase2_Step6_best model.pkl` | Serialized final tuned Random Forest model |
| `Phase2_Step7_v2.ipynb` | Model comparison and consensus scoring across the full candidate variant pool |
| `Phase2_Step8_v2.ipynb` | SHAP interpretability analysis (TreeExplainer, scatter/bar/waterfall visualizations) |
| `Phase2_Step9_SpliceAI.ipynb` | Targeted SpliceAI Lookup validation for the four HIGH-impact splice-region candidates (SPICE1 ×2, TNRC18, PAPOLA) |
| `Phase2_Variants_and_Scores.ipynb` | Consolidated 22-candidate table with consensus pathogenicity scores and SpliceAI comparison |
| `Validation_MLcheck_Liftover.ipynb` | GRCh37→GRCh38 coordinate liftover and cross-checks |

*(Phase 1 read processing, alignment, and variant calling were performed on the Galaxy platform — FastQC, Trimmomatic, BWA-MEM, GATK/FreeBayes, SnpEff v5.4c, GEMINI — and are not represented as notebooks in this repository.)*

## Methods Summary

WES reads (GRCh37/hg19) were processed via a Galaxy pipeline following the GTN exome-sequencing tutorial, yielding 22 candidate off-target variants meeting HIGH/MODERATE impact and novelty criteria out of an initial >9 million annotated variants. In Phase 2, real CADD Phred scores were retrieved for ClinVar training data via MyVariant.info, and Random Forest and XGBoost classifiers restricted to this single feature were trained and cross-validated to avoid feature-derived data leakage. Tuned models were applied to both the full ~1.03M-variant candidate pool and the original 22 Phase 1 candidates, with SHAP used for interpretability and SpliceAI used as an independent splice-specific validation layer.

## Software & Tools

Galaxy (FastQC, MultiQC, Trimmomatic, BWA-MEM, GATK, FreeBayes, SnpEff v5.4c, GEMINI) · Python 3 / Google Colab (pandas 2.2.2, scikit-learn 1.6.1, XGBoost 3.2.0, SHAP 0.52.0) · MyVariant.info API · SpliceAI Lookup (Broad Institute) · py-liftover

## Author

Tanvi Sakore (T.Y. B.Sc. Biotechnology), under the guidance of Dr. Nilofer Shaikh — AI/ML in Bioinformatics internship project, Biotecnika Info Labs.

## Citation

*Manuscript in preparation for submission.*
