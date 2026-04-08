# ECIF-KINASE-AFFINITY-PREDICTOR
This repository contains Jupyter notebooks and curated datasets for predicting protein–ligand binding affinity in kinase complexes. The workflow integrates Extended Connectivity Interaction Features (ECIF), RDKit ligand descriptors, and classical machine learning regression models to estimate binding affinity.

The pipeline includes data curation, structure preprocessing, feature generation, model training, evaluation, and external prediction using an independent kinase dataset.

📌 Project Overview

This workflow focuses on structure-based binding affinity prediction for protein kinases and includes:

Curation of kinase protein–ligand complexes from PDBbind v2020 for training and RCSB PDB for external evaluation
Preprocessing of protein–ligand structures for consistent cheminformatics input
Generation of ECIF interaction features across multiple distance cutoffs
Calculation of RDKit ligand descriptors
Construction of ECIF-only and ECIF + RDKit merged feature matrices
Hyperparameter tuning and training of multiple regression models
Prediction of pIC / binding affinity values
Performance evaluation using MSE, RMSE, R², and Pearson Correlation Coefficient (PCC)
External prediction for 5,265 non-overlapping kinase complexes
🧪 Methodology
1. Data Collection and Preprocessing
Purpose

This step involves the collection, curation, and preprocessing of kinase protein–ligand complexes for both model training and external validation.

Training Dataset

Training structures and affinity values were derived from PDBbind v2020, which contains approximately 14,126 protein–ligand complexes in the general set.

A kinase-focused subset was curated using filters such as:

Protein name
UniProt information
PDB ID
Ligand identity
Resolution criteria

This curation yielded:

2,494 kinase complexes at the initial download stage
External Dataset

An independent external kinase dataset was collected from the RCSB Protein Data Bank (PDB) using protein kinase–related search and curation strategies.

The structures were downloaded using a batch bash workflow, resulting in:

6,802 complexes successfully downloaded
Structure Processing

Protein–ligand complexes were processed using Schrödinger Maestro:

Complexes were split into receptor and ligand
Ligands were converted to SDF
Proteins were saved as PDB

This ensured compatibility with the downstream feature generation workflow.

Final Dataset Counts
Training Set

After ligand standardization and descriptor generation:

2,401 ligands remained after RDKit preprocessing
2,337 kinase complexes were retained after ECIF and merged descriptor generation
External Set

After structure processing and feature generation:

6,800 protein PDB files
6,240 ligand files
5,265 external kinase complexes retained after ECIF and ECIF–RDKit merged descriptor generation
Training Structure Directory

Final curated training structures are stored as:

PDB_general_kinase_training_2337/
Activity Labels

File: training_activity.csv

This file contains the experimentally derived activity values for the 2,337 final training complexes:

Complex_ID
pIC

It represents the filtered affinity table aligned with the final modeling cohort, rather than the full PDBbind release.

2. Descriptor Calculation

Notebook: descriptor_generation.ipynb

Purpose

This notebook is used to:

Standardize ligands using RDKit
Compute ECIF protein–ligand interaction features
Calculate RDKit ligand descriptors
Merge ECIF and ligand descriptors into model-ready feature tables
Feature Types
ECIF Features

ECIF encodes protein–ligand atom-type interaction counts within specified distance cutoffs.

1,540 features per cutoff
Distance cutoffs typically range from:
4.0 Å to 15.0 Å
in 0.5 Å increments
RDKit Ligand Descriptors

Ligand-specific physicochemical and structural descriptors are calculated using RDKit.

Key Steps
Load preprocessed protein PDB and ligand SDF files
Standardize and sanitize ligands using RDKit
Explicit hydrogen handling
Aromaticity assignment
Molecule sanitization
Generate:
ECIF feature matrices
RDKit ligand descriptor matrices
Merge descriptor blocks into final feature tables
Output
Feature matrices (CSV format)
ECIF-only descriptor tables
ECIF + RDKit merged descriptor tables
Descriptor sets aligned with:
2,337 training complexes
5,265 external complexes
3. Model Training and Evaluation

Notebook: ecif_models.ipynb

Purpose

This notebook is used to:

Train regression models on:
ECIF-only features
ECIF + RDKit merged descriptors
Perform hyperparameter tuning
Compare model performance across descriptor types
Predict binding affinity / pIC values
Machine Learning Models

The following regression models are used:

Decision Tree (DT)
Support Vector Regressor (SVR)
Random Forest (RF)
Gradient Boosting Regressor (GBR)
Extreme Gradient Boosting (XGB)
Key Steps
Load:
ECIF-only feature tables
ECIF + RDKit merged feature tables
training_activity.csv
Prepare training and validation splits
Perform hyperparameter tuning
Train regression models for each descriptor type
Evaluate performance using:
Mean Squared Error (MSE)
Root Mean Squared Error (RMSE)
Coefficient of Determination (R²)
Pearson Correlation Coefficient (PCC)
Output
Trained regression models
Predicted pIC values
Performance metrics
Result tables and visualizations generated in the notebook
Note

This repository may include saved ECIF-only models, while ECIF + RDKit merged models may have been trained and evaluated in the notebook but are not necessarily distributed as serialized files.

4. External Prediction
Purpose

This step is used to predict binding affinity (pIC) values for an independent external kinase dataset.

External Dataset

Predictions were performed on:

5,265 non-overlapping kinase complexes

These external complexes were evaluated using the same descriptor-generation workflow as the training set.

Prediction Settings

Predictions were generated in two parallel settings:

ECIF-only descriptor models
ECIF + RDKit merged descriptor models

This allows direct comparison of the predictive utility of:

Protein–ligand interaction features alone
Interaction features combined with ligand descriptors
Output
Predicted pIC / affinity values for external kinase complexes
Model-wise external prediction results
🛠️ Requirements

This workflow was developed in Python using Jupyter Notebook.

Core Dependencies
Python ≥ 3.8
Jupyter Notebook
RDKit (recommended: 2020.03.x for consistency with ECIF ligand typing used in the study)
pandas
numpy
scikit-learn
XGBoost
Optional Dependency
Schrödinger Maestro
Required only if you want to fully reproduce the PDB → split → MAE → SDF/PDB preprocessing workflow
