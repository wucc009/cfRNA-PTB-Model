# cfRNA-PTB-Model

Code and data for the paper: **"Multidimensional cfRNA response modeling identifies a 5-gene pair signature for high-robust pulmonary tuberculosis diagnosis"**

---

## 🚀 Overview

This repository contains: (i) the training, testing, and validation datasets used in the study; (ii) a complete computational pipeline for developing a 5-gene pair signature for PTB diagnosis using cfRNA data (covering feature extraction, model selection, feature selection, hyperparameter optimization, model construction and model validation); and (iii) reproducible scripts to retrieve performance metrics of the parent study model on the validation cohort.

## 📂 Project Structure

```text
├── Figure scripts/                    # Executable scripts to reproduce the main figures       
│   ├── Input/
│       ├── 01_PTB_limma_results.csv
│       ├── 02_cell_proportion.csv
│       ├── 03_MDP_sample_scores.tsv
│       ├── 04_GSE255071_series_matrix.xlsx
│       ├── 05_GSE255071_colnames.txt
│       ├── 06_GO_enrichment_results.csv
│       ├── 07_DEG_model_select.csv
│       ├── 08_cfRNA_vs_wbRNA.csv
│       └── 09_5_gene-pair_scores.csv
│   ├── Output
│       ├── 01_PTB_limma_results_volcano.pdf
│       ├── 02_average_cell_proportions.pdf
│       ├── 03_MDP_scores_bar_plot.pdf
│       ├── 04_MDP_scores_bar_plot.pdf
│       ├── 05_MDP_scores_semi-quantitative_kendall_correlation_plot.pdf
│       ├── 06_MDP_scores_box_plot.pdf
│       ├── 07_GO_enrichment_plot.pdf
│       ├── 08_DEG_model_select_barplot.pdf
│       ├── 09_Sensitivity.pdf
│       └── 10_qXR.pdf      
│   ├── Figure 1.ipynb               
│   ├── Figure 2.ipynb
│   ├── Figure 3.ipynb           
│   ├── Figure 4.ipynb                   
│   └── Figure 5.ipynb
│
├── Parent-study validation performance extraction/                            
│   ├── AUC.jpg                
│   ├── Parent-study validation performance extraction.ipynb                 
│   └── Sample_size.jpg              
│
├── data/                              # Datasets
│   ├── 01_train_df.csv                # Training cohort
│   ├── 02_test_df.csv                 # Testing cohort
│   └── 03_E_valid_df.csv              # Validation cohort
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
├── Gene-pair_modeling.ipynb           # Main analysis script
│
└── environment.yml
```

## 🛠️ Reproduction Guide
To ensure the findings of this study are fully reproducible, we provide the following step-by-step guide for setting up the environment and executing the computational pipeline.  

### 1. Environment Setup

The analysis was performed in a Linux environment using Conda for dependency management.  
We have provided an `environment.yml` file containing all necessary libraries (e.g., `joblib`, `scikit-learn`, and R dependencies).

To recreate the exact environment, run the following commands in your terminal:

```bash
# Clone the repository
git clone https://github.com/wucc009/cfRNA-PTB-Model.git
cd cfRNA-PTB-Model

# Create the conda environment (Python 3.10.13 with scikit-learn 1.7.0)
conda env create -f environment.yml

# Activate the environment
conda activate cfRNA_PTB_Model

# Verify installation
python -c "import sklearn; print(sklearn.__version__)"  # Should output 1.7.0
```

### 2. Repository Content

After cloning this repository or downloading the files, you will find the following core components:

- `Gene-pair modeling.ipynb`: The primary end-to-end computational pipeline (Python).

- `data/`: Contains the cfRNA datasets for Training, Testing, and Validation cohorts.

- `result/`: Directory where all intermediate files, trained models, and performance figures are stored.

- `Figure scripts/`: Scripts to reproduce the main figures.       

- `Parent-study validation performance extraction/`: Scripts to retrieve performance metrics of the parent study model on the validation cohort.

### 3. Step-by-Step Analysis Workflow

Open `Gene-pair_modeling.ipynb` in Jupyter Notebook/Lab and execute cells sequentially. The pipeline consists of the following key steps:

#### Step 1: Gene-Pair Feature Extraction

- **Input:** `data/01_train_df.csv` (training data).
- **Operations:** Converts single-gene expression into gene-pair relative expression features.
- **Output:** `result/01_gene_pairs.csv` (list of selected gene pairs).

#### Step 2: Model Selection

- **Input:** `data/01_train_df.csv` (training data) and `result/01_gene_pairs.csv` (selected gene pairs from Step 1).  
- **Operations:**
  - Encode gene pairs as binary features (relative expression comparison).
  - Train and evaluate multiple classifiers (KNN, Naive Bayes, LR, SVM, XGBoost, RF, MLP, CNN) using cross-validation and ensemble methods.
  - Select the best-performing model for downstream analysis.
- **Output:** `result/02_feature_coding_data.csv`, `result/03_model_select.csv`.

#### Step 3: Feature Selection

- **Input:** `result/01_gene_pairs.csv` (selected gene pairs from Step 1) and `result/02_feature_coding_data.csv` (encoded feature matrix from Step 2). 
- **Operations:**
  - Focusing on the optimal model selected in Step 2.
  - Rank features by importance using MIC, GBDT, and Relief algorithms; compute TSP scores.
  - Perform incremental feature selection to identify the optimal feature subset via cross-validation.  
- **Output:** `result/04_feature_importance_rank.csv`, `result/05_incremental_feature_selection.csv`, `result/06_incremental_feature_selection.pdf`.
  
#### Step 4: Hyperparameter Optimization and Model Construction

- **Input:** `result/02_feature_coding_data.csv` (encoded feature matrix from Step 2), `result/04_feature_importance_rank.csv` (selected features from Step 3).  
- **Operations:**
  - Based on the optimal model from Step 2 and the optimal feature subset identified in Step 3.
  - Perform randomized search cross-validation for hyperparameter tuning, and train the final optimized model.
- **Output:** `result/07_best_model.joblib`.

#### Step 5: Model Validation

- **Input:**
  - `data/02_test_df.csv` (test set).
  - `data/03_E_valid_df.csv` (validation set).
  - `result/01_gene_pairs.csv` (selected gene pairs from Step 1).
  - `result/02_feature_coding_data.csv` (encoded feature matrix from Step 2).
  - `result/04_feature_importance_rank.csv` (selected features from Step 3).  
  - `result/07_best_model.joblib` (trained optimal model from Step 4).
- **Operations:** Encode features for test and validation sets, perform predictions using the optimized model, calculate performance metrics (AUC) and generate ROC curves.  
- **Output:** `result/08_test_feature_coding_data.csv`, `result/09_val_feature_coding_data.csv`, `result/10_gene_pair_model_AUC.pdf`.
