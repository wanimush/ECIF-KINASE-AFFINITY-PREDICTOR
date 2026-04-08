# ECIF-KINASE-AFFINITY-PREDICTOR
This repository contains Jupyter notebooks and curated datasets for predicting protein–ligand binding affinity in kinase complexes. The workflow integrates Extended Connectivity Interaction Features (ECIF), RDKit ligand descriptors, and classical machine learning regression models to estimate binding affinity.

The pipeline includes data curation, structure preprocessing, feature generation, model training, evaluation, and external prediction using an independent kinase dataset.

# CXCR Inhibitor Prediction using Machine Learning

This repository contains a Python Jupyter Notebook implementing a machine
learning–based pipeline for predicting CXCR-targeted inhibitors using
RDKit-derived molecular fingerprints and descriptors.

The workflow integrates cheminformatics feature generation with classical
machine learning models for compound activity prediction.

---

## 📌 Project Overview

- Molecular feature generation using **RDKit**
- Fingerprint-based representation of small molecules
- Supervised machine learning models for activity prediction
- Focus on **CXCR receptor–related inhibitor screening**

---

## 🧪 Methodology

1. Descriptor Calculation  
**File:** `Script_descriptor_calcualation_CXCR4.ipynb`

### Purpose
- Calculate **RDKit molecular descriptors** from SMILES strings
- Generate a descriptor matrix for CXCR4 datasets
- Save descriptor names for **reproducibility**
- Prepare data for model training and prediction

### Key Steps
1. Load dataset containing SMILES
2. Generate RDKit descriptors
3. Handle invalid molecules
4. Save:
   - Descriptor DataFrame
   - Descriptor name list (`.pkl`)
   
### Output
- Descriptor matrix (`.csv`)
- Pickled descriptor names (`descriptor_names.pkl`)

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

-Python ≥ 3.8 (match your environment)

-Jupyter Notebook

-RDKit (e.g. 2020.03.x for consistency with ECIF ligand typing in the paper)

-pandas, numpy

-scikit-learn

-XGBoost

-Schrödinger Maestro (optional, for reproducing PDB → split → MAE → SDF/PDB steps)

### Install dependencies (recommended via conda):
```bash
conda install -c conda-forge rdkit scikit-learn pandas numpy matplotlib


