# ECIF-KINASE-AFFINITY-PREDICTOR

This repository contains Jupyter notebooks and curated datasets for predicting protein–ligand binding affinity in kinase complexes. The workflow integrates Extended Connectivity Interaction Features (ECIF), RDKit ligand descriptors, and classical machine learning regression models to estimate binding affinity.

The pipeline includes data curation, structure preprocessing, feature generation, model training, evaluation, and external prediction using an independent kinase dataset.

---

## 📌 Project Overview

This workflow focuses on ML based binding affinity prediction for protein kinases and includes:

- Curation of kinase protein–ligand complexes from PDBbind v2020 for training and RCSB PDB for external evaluation
- Preprocessing of protein–ligand structures for consistent cheminformatics input
- Generation of ECIF interaction features across multiple distance cutoffs
- Calculation of RDKit ligand descriptors
- Construction of ECIF-only and ECIF + RDKit merged feature matrices
- Hyperparameter tuning and training of multiple regression models
- Prediction of pIC / binding affinity values
- Performance evaluation using MSE, RMSE, R², and Pearson Correlation Coefficient (PCC)
- External prediction for 5,265 non-overlapping kinase complexes

---

## 🧪 Methodology

# 1. Data collection & preprocessing

### Purpose
- This step involves the collection, curation, and preprocessing of kinase protein–ligand complexes for both model training and external validation.

-- Training Dataset
- Training structures and affinity values were derived from PDBbind v2020, which contains approximately 14,126 protein–ligand complexes in the general set.
- A kinase-focused subset was curated using filters such as:
- Protein name
- UniProt information
- PDB ID
- Ligand identity
- Resolution criteria
This curation yielded:
- 2,494 kinase complexes at the initial download stage

-- External Dataset
- An independent external kinase dataset was collected from the RCSB Protein Data Bank (PDB) using protein kinase–related search and curation strategies
- The structures were downloaded using a batch bash workflow, resulting in:
- 6,802 complexes successfully downloaded

-- Structure Processing
- Protein–ligand complexes were processed using Schrödinger Maestro:
- Complexes were split into receptor and ligand
- Ligands were converted to SDF
- Proteins were saved as PDB
- This ensured compatibility with the downstream feature generation workflow.
-- Final Dataset Counts
- Training Set
- After ligand standardization and descriptor generation:
- 2,401 ligands remained after RDKit preprocessing
- 2,330 kinase complexes were retained after ECIF and merged descriptor generation

**File:** `ECIF_with_activity_train.csv` and `Merged_ECIF_RDKit_train_full.csv`
 
-- External Set
- After structure processing and feature generation:
- 6,800 protein PDB files
- 6,240 ligand files
- 5,265 external kinase complexes retained after ECIF and ECIF–RDKit merged descriptor generation

**File:** `ECIF_descriptors_test_clean.csv` and `Merged_ECIF_RDKit_test_nonoverlapped.csv`

*********************************************************************************************************

# 2. Descriptor Calculation  

**File:** `descriptor_generation.ipynb`

### Purpose
- Standardize ligands with RDKit (explicit hydrogens, aromaticity, sanitization).
- Compute ECIF features (protein–ligand atom-type pair counts within distance cutoffs r; 1,540 features per cutoff; cutoffs e.g. 4.0–15.0 Å, 0.5 Å steps as in the study).
- Compute RDKit ligand descriptors and merge with ECIF for downstream ML.

### Key Steps
- Load preprocessed protein PDB and ligand SDF inputs.
- Run ligand preprocessing / sanitization as required for consistent atom typing.
- Generate ECIF and RDKit descriptor blocks and merge into feature tables.
   
### Output
- Feature matrices (e.g. merged ECIF + RDKit CSV files produced in the notebook workflow).
- Descriptor sets aligned with 2,330 training complexes and 5,265 external complexes after the full pipeline.

*********************************************************************************************************

# 3. Model training & evaluation 

**File:** `model_development.ipynb`

### Purpose
-- This notebook is used to:
- Train regression models on:
- ECIF-only features
- ECIF + RDKit merged descriptors
- Perform hyperparameter tuning
- Compare model performance across descriptor types
- Predict binding affinity / pIC values

### Machine Learning Models
- Decision Tree (DT)
- Support Vector Regressor (SVR)
- Random Forest (RF)
- Gradient Boosting Regressor (GBR)
- Extreme Gradient Boosting (XGB)

### Key Steps
1. Load:
- ECIF-only feature tables
- ECIF + RDKit merged feature tables
**File:** `ECIF_with_activity_train.csv` and `Merged_ECIF_RDKit_train_full.csv`
2. Prepare training and validation splits
3. Perform hyperparameter tuning
4. Train regression models for each descriptor type
5. Evaluate performance using:
- Mean Squared Error (MSE)
- Root Mean Squared Error (RMSE)
- Coefficient of Determination (R²)
- Pearson Correlation Coefficient (PCC)

### Output
- Trained regression models
- Predicted pIC values
- Performance metrics
- Result tables and visualizations generated in the notebook
### Note
- This repository may include saved ECIF-only models, while ECIF + RDKit merged models may have been trained and evaluated in the notebook but are not necessarily distributed as serialized files.

*********************************************************************************************************

# 4. External Prediction 

**File:** `model_development.ipynb`

### Purpose
- This step is used to predict binding affinity (pIC) values for an independent external kinase dataset.
- Generate predictions from multiple models
-- Predictions were generated in two parallel settings:
- ECIF-only descriptor models
- ECIF + RDKit merged descriptor models

### Output
- Predicted pIC / affinity values for external kinase complexes
- Model-wise external prediction results

**File:** `Predictions_best_3_ECIF+RDKIT.csv`

*********************************************************************************************************

## 🛠️ Requirements

The notebook was developed using Python and requires the following libraries:

- Anaconda (24.11.0, 64-bit)
- Jupyter Notebook (6.4.6)
- Python ≥ 3.8  
- RDKit  (2020.03.1)
- scikit-learn (1.5.2)  
- pandas  
- numpy  
- Schrödinger Maestro (optional, for reproducing PDB → split → MAE → SDF/PDB steps)

### Install dependencies (recommended via conda):
```bash
conda install -c conda-forge rdkit scikit-learn pandas numpy matplotlib


