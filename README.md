# ECIF-KINASE-AFFINITY-PREDICTOR
Machine learning framework for predicting kinase–ligand binding affinities using ECIF and RDKit descriptors. This repository includes workflows for feature generation, model training, validation, external predictions, and consensus-based prioritization of high-confidence kinase-binding compounds for virtual screening applications
This repository contains Jupyter notebooks and curated data for predicting protein–ligand binding affinity on kinase complexes. The pipeline combines Extended Connectivity Interaction Features (ECIF) with RDKit ligand descriptors and classical regression models.
The workflow covers PDBbind-derived training data, preprocessing and feature generation, model fitting, and a separate external kinase set from RCSB PDB.
Project Overview
●	Curate kinase protein–ligand complexes from PDBbind v2020 (training) and RCSB PDB (external evaluation).
●	Preprocess structures (split, convert, RDKit ligand standardization) for consistent cheminformatics input.
●	Compute ECIF protein–ligand interaction features across distance cutoffs and RDKit ligand descriptors.
●	Build merged ECIF + RDKit descriptor matrices for machine learning alongside ECIF-only feature sets.
●	Hyperparameter-tune and train regression models (DT, SVR, RF, GBR, XGB) on ECIF-only and merged inputs.
●	Predict pIC / binding affinity and assess models with MSE, RMSE, R², and Pearson correlation (PCC).
●	Generate external pIC predictions for 5,265 non-overlapped kinase complexes using saved ECIF-only and ECIF+RDKit models.
●	Focus on structure-based binding affinity estimation for protein kinases.
Methodology
1. Data collection & preprocessing
Scope: Structures and affinities for training come from PDBbind 2020 (general set on the order of 14,126 complexes). Kinase-focused curation (keywords and filters on protein name, UniProt, PDB ID, ligand, resolution) yields 2,494 complexes at the download stage. External complexes are identified via RCSB (e.g. “Protein Kinase” search and curation); coordinates are fetched with the PDB batch download (bash) workflow—6,802 complexes downloaded successfully.
Splitting & formats: Complexes are split into receptor and ligand with Schrödinger Maestro; ligands are converted to SDF and proteins to PDB for the feature pipeline.
Counts (training): After RDKit ligand standardization and filtering, 2,401 ligands remain; after ECIF and merged descriptor generation, 2,337 training complexes are retained.
Counts (external): After Maestro split there are 6,800 protein PDBs and 6,240 ligand files; counts are unchanged after RDKit standardization. After ECIF and ECIF–RDKit merged descriptors, 5,265 external complexes are used for evaluation.
Training structures: Per-complex folders for the final 2,337 training set:
PDB_general_kinase_training_2337/
Activity labels (training only):  
File: training_activity.csv  
Binding values (`Complex_ID`, `pIC`) for the 2,337 modeling complexes only. The full PDBbind 2020 affinity index covers the wider release; this CSV is the filtered table aligned with the final training cohort.

2. Descriptor calculation
File: descriptor_generation.ipynb
Purpose
●	Standardize ligands with RDKit (explicit hydrogens, aromaticity, sanitization).
●	Compute ECIF features (protein–ligand atom-type pair counts within distance cutoffs r; 1,540 features per cutoff; cutoffs e.g. 4.0–15.0 Å, 0.5 Å steps as in the study).
●	Compute RDKit ligand descriptors and merge with ECIF for downstream ML.
Key steps
1.	Load preprocessed protein PDB and ligand SDF inputs.
2.	Run ligand preprocessing / sanitization as required for consistent atom typing.
3.	Generate ECIF and RDKit descriptor blocks and merge into feature tables.
Output
●	Feature matrices (e.g. merged ECIF + RDKit CSV files produced in the notebook workflow).
●	Descriptor sets aligned with 2,337 training complexes and 5,265 external complexes after the full pipeline.

3. Model training & evaluation
File: ecif_models.ipynb
Purpose
●	Train regression models on ECIF-only features and, in parallel, on ECIF + RDKit merged descriptors.
●	For both input types, the same model families undergo hyperparameter tuning and final training in the same way (see the notebook).
●	Predict binding-related targets (e.g. pIC) and report MSE, RMSE, R², and PCC.
Note: This repository may ship saved model files for ECIF-only models only; the merged-descriptor models were built and evaluated equivalently but are not necessarily included here.
Machine learning models
●	Decision Tree (DT)
●	Support Vector Regressor (SVR)
●	Random Forest (RF)
●	Gradient Boosting Regressor (GBR)
●	XGBoost (XGB)
Key steps
4.	Load feature tables (ECIF-only and/or ECIF–RDKit merged) and activity labels (training_activity.csv for the 2,337 training set).
5.	Split data (e.g. train/validation as implemented in the notebook).
6.	Hyperparameter tune, train, and compare regressors for each descriptor type; compute evaluation metrics.
Output
●	Model results, predictions, and metric summaries as saved in the notebook (e.g. tables/plots/exports—see notebook cells).

4. External prediction
For the 5,265 non-overlapped external complexes (after ECIF and merged descriptor generation), pIC values were predicted using saved, trained models in two settings: (i) ECIF-only descriptors and (ii) ECIF + RDKit merged descriptors—analogous to the two model lines tuned and fitted in Step 3.
Requirements
Developed with Python in Jupyter. Typical dependencies:
●	Python ≥ 3.8 (match your environment)
●	Jupyter Notebook
●	RDKit (e.g. 2020.03.x for consistency with ECIF ligand typing in the paper)
●	pandas, numpy
●	scikit-learn
●	XGBoost
●	Schrödinger Maestro (optional, for reproducing PDB → split → MAE → SDF/PDB steps)

Install dependencies (example with conda)
conda install -c conda-forge rdkit scikit-learn pandas numpy matplotlib xgboost jupyter
