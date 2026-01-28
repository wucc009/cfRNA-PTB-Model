# cfRNA-PTB-Model

Code and data for the paper: **"Multidimensional cfRNA response modeling identifies a 5-gene pair signature for high-robust pulmonary tuberculosis diagnosis"**

---

## 🚀 Overview

This repository contains: (i) the training, testing, and validation datasets used in the study; (ii) a complete computational pipeline for developing a 5-gene pair signature for PTB diagnosis using cfRNA data (covering feature extraction, model selection, feature selection, hyperparameter optimization, model construction and model validation); and (iii) reproducible scripts to retrieve performance metrics of the parent study model on the validation cohort.

## 📂 Project Structure

```text
├── Parent-study validation performance extraction/                            
│   ├── AUC.jpg                
│   ├── Parent-study validation performance extraction.ipynb                 
│   └── Sample_size.jpg              
│
├── data/                              # datasets
│   ├── 01_train_df.csv                # Training cohort
│   ├── 02_test_df.csv                 # Internal testing cohort
│   └── 03_E_valid_df.csv              # External validation cohort
│
├── result/                            # Output files from the pipeline
│   ├── 01_gene_pairs.csv              
│   ├── 02_feature_coding_data.csv     
│   ├── 03_model_select.csv            
│   ├── 04_feature_importance_rank.csv 
│   ├── 05_incremental_feature_selection.csv 
│   ├── 06_incremental_feature_selection.pdf 
│   ├── 07_best_model.joblib          
│   ├── 08_test_feature_coding_data.csv 
│   ├── 09_val_feature_coding_data.csv  
│   └── 10_gene_pairs_model_AUC.pdf    
│
└── Gene-pair_modeling.ipynb           # Main analysis script
