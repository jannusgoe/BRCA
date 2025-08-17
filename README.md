# Breast Cancer Treatment Response Prediction using RNA-seq and Machine Learning

This project analyzes RNA-seq data from breast cancer patients to predict chemotherapy treatment response using machine learning approaches. The analysis combines differential gene expression analysis with supervised learning to identify predictive biomarkers.


AI-generated Description:
## Project Overview

The project uses RNA-seq data from 22 breast cancer patients to build predictive models that classify patients as either "Resistant" or "Sensitive" to chemotherapy treatment. The analysis includes:

- Differential gene expression analysis using DESeq2
- Feature selection to handle high-dimensional genomic data
- Multiple machine learning approaches (Elastic Net, Random Forest)
- Integration of clinical features with genomic data
- Cross-validation for robust performance evaluation

## Dataset

- **Source**: GEO/SRA dataset (BioProject: PRJNA680808)
- **Samples**: 22 breast cancer patients
- **Data Type**: RNA-seq gene expression data
- **Outcome**: Treatment response (Resistant vs Sensitive)
- **Features**: 
  - Gene expression counts for ~39,000 genes
  - Clinical variables: age, molecular subtype, treatment regimen
  - Parsed treatment features: drug types, cycle counts

## File Structure

```
.
├── README.md                           # This file
├── brca.ipynb                         # Main analysis notebook (R)
├── brca.html                          # HTML output of the notebook
├── multiqc_report.html                # Quality control report
├── renv.lock                          # R environment lock file
├── sample_treatment_data.csv          # Treatment data for LLM parsing
└── data/
    ├── SraRunTable.txt               # Sample metadata from SRA
    ├── salmon.merged.gene_counts.tsv # Raw gene count matrix
    ├── salmon.merged.gene_tpm.tsv    # TPM-normalized expression
    └── treatment_parsed.csv          # Structured treatment features
```

## Analysis Workflow

### 1. Data Preprocessing
- Load gene expression counts and patient metadata
- Quality filtering of low-count genes
- Variance stabilizing transformation (VST) for ML preparation

### 2. Differential Expression Analysis
- DESeq2 analysis comparing Resistant vs Sensitive patients
- Multiple testing correction using Benjamini-Hochberg
- Volcano plot visualization of significant genes

### 3. Feature Selection
- **Challenge**: 23,000+ genes vs 22 samples (high dimensionality)
- **Solution**: Select top 110 genes by variance (unsupervised approach)
- **Rationale**: Avoid data leakage while maintaining 5:1 feature-to-sample ratio

### 4. Machine Learning Models

Three models were developed and compared:

1. **Elastic Net Regression**
   - Linear model with L1/L2 regularization
   - Automatic feature selection through penalization
   - Best performance: ROC-AUC = 0.933

2. **Random Forest (Genes Only)**
   - Tree-based ensemble method
   - Captures non-linear relationships
   - Performance: ROC-AUC = 0.533

3. **Random Forest + Clinical Data**
   - Incorporates clinical features (age, molecular subtype, treatment components)
   - Performance: ROC-AUC = 0.400

### 5. Model Evaluation
- 5-fold stratified cross-validation
- ROC-AUC as primary performance metric
- Hyperparameter tuning using grid search

## Key Results

### Model Performance
| Model | Cross-Validated ROC-AUC | Standard Error |
|-------|------------------------|----------------|
| Elastic Net | 0.933 | 0.067 |
| Random Forest | 0.533 | 0.162 |
| RF + Clinical | 0.400 | 0.172 |

### Feature Importance Findings

- **Elastic Net** identified 20 most predictive genes based on coefficient magnitude
- **Overlap Analysis**: Only 5 genes overlap between top ML features and DESeq2 significant genes
- **Implication**: Statistical significance ≠ predictive power in machine learning context

### Clinical Feature Integration
- Adding clinical variables did not improve Random Forest performance
- Suggests gene expression data alone captures treatment response patterns
- Clinical features may be redundant with molecular signatures

## Dependencies

This project uses R 4.5.0 with the following key packages:

### Core Analysis
- `tidyverse` - Data manipulation and visualization
- `DESeq2` - Differential expression analysis
- `tidymodels` - Machine learning framework

### Specialized Packages
- `ranger` - Random Forest implementation
- `glmnet` - Elastic Net regression
- `vip` - Variable importance plots
- `patchwork` - Plot composition
- `matrixStats` - Matrix operations

### Environment Management
The project uses `renv` for reproducible package management. The `renv.lock` file contains exact package versions.

## Usage

### Prerequisites
1. Install R 4.5.0 or later
2. Install required packages (see renv.lock for exact versions)

### Running the Analysis
1. Open `brca.ipynb` in a Jupyter environment with R kernel, or
2. Convert notebook to R script and run in RStudio/R console
3. Ensure all data files are in the `data/` directory

### Reproducing Results
```r
# Restore package environment
renv::restore()

# Run the complete analysis
# Execute cells in brca.ipynb sequentially
```