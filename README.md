# ECIF-KINASE-AFFINITY-PREDICTOR

This repository contains Jupyter notebooks and curated datasets for predicting protein–ligand binding affinity in kinase complexes. The workflow integrates Extended Connectivity Interaction Features (ECIF), RDKit ligand descriptors, and classical machine learning regression models to estimate binding affinity.

The pipeline includes data curation, structure preprocessing, feature generation, model training, evaluation, and external prediction using an independent kinase dataset.

---

## 📌 Project Overview

This workflow focuses on structure-based binding affinity prediction for protein kinases and includes:

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

1. Data collection & preprocessing

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

-- External Set
- After structure processing and feature generation:
- 6,800 protein PDB files
- 6,240 ligand files
- 5,265 external kinase complexes retained after ECIF and ECIF–RDKit merged descriptor generation

**File:**training_activity.csv

2. Descriptor Calculation  
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
- Descriptor sets aligned with 2,337 training complexes and 5,265 external complexes after the full pipeline.






2. Model Training & Feature Selection  
**File:** `Script_training_models_CXCR4.ipynb`

### Purpose
- Perform **feature selection** using Boruta / RF
- Scale features using `StandardScaler`
- Train multiple ML classifiers
- Save trained models and preprocessing objects

### Machine Learning Models
- Random Forest (RF)
- Support Vector Machine (SVM)
- Gradient Boosting (GB)
- AdaBoost (AB)
- Logistic Regression (LR)
- XGBoost / CatBoost (if enabled)

### Key Steps
1. Load descriptor dataset
2. Split data into train/test
3. Feature selection (Boruta)
4. Feature scaling
5. Model training & evaluation
6. Save objects using `pickle` / `joblib`

### Output Files
- `selected_features.pkl`
- `scaler.pkl`
- `model_rf.pkl`
- `model_svm.pkl`
- `model_gb.pkl`
- (other trained models)

---

3. External Compound Prediction  
**File:** `script_external_predictions.ipynb`

### Purpose
- Predict CXCR4 activity for **external / novel compounds**
- Use **exact same descriptors, features, and scaling**
- Generate predictions from multiple models

*********************************************************************************************************

Prediction Workflow (Step-by-Step)

Step 1: Prepare External Dataset
Your input file must contain: SMILES

Step 2: Calculate Descriptors
- Use `Script_descriptor_calcualation_CXCR4.ipynb`
- Ensure **same RDKit version** as training
- Output: descriptor DataFrame

Step 3: Load Saved Objects
import pickle
import joblib

with open("descriptor_names.pkl", "rb") as f:
    descriptor_names = pickle.load(f)

with open("selected_features.pkl", "rb") as f:
    selected_features = pickle.load(f)

scaler = joblib.load("scaler.pkl")
model_rf = joblib.load("model_rf.pkl")

Step 4: Preprocess External Data
X = df_descriptors[descriptor_names]
X_selected = X[selected_features]
X_scaled = scaler.transform(X_selected)

Step 5: Generate Predictions
df_predictions = pd.DataFrame()

df_predictions["DT"] = dt_model.predict(X_scaled)
df_predictions["Dt_prob"] = dt_model.predict_proba(X_scaled)[:,1]

Step 6: Save Prediction Results
df_predictions.to_csv("CXCR4_external_predictions.csv", index=False)

***************************************************************************************************************
## 🛠️ Requirements

The notebook was developed using Python and requires the following libraries:

- Anaconda (24.11.0, 64-bit)
- Jupyter Notebook (6.4.6)
- Python ≥ 3.8  
- RDKit  (2024.3.6)
- scikit-learn (1.5.2)  
- pandas  
- numpy  
- Boruta (0.4.3)

### Install dependencies (recommended via conda):
```bash
conda install -c conda-forge rdkit scikit-learn pandas numpy matplotlib


