# UNDERCANOPY: Consistent and Replicable Estimation of Bilateral Climate Finance and its determinant

This repository hosts a comprehensive, multi-method framework for the estimation and classification of bilateral climate finance and its determinant. Developed to meet high academic standards of transparency and reproducibility, UNDERCANOPY integrates data preprocessing, advanced classification, and forecasting techniques to generate robust insights into climate finance flows.

The project builds on the methodology of Toetzke et al. (2022) (see their repository [here](https://github.com/MalteToetzke/consistent-and-replicable-estimation-of-bilateral-climate-finance/tree/main)) and has been designed to be fully replicable by other researchers.

---

## Repository Overview

UNDERCANOPY is organized into several interrelated components:

### 1. Data Preprocessing and Consolidation (R Scripts)
- **UploadBase.R:**  
  - Loads raw OECD CRS data (annual and multi-year text files), [available here](https://data-explorer.oecd.org/vis?fs[0]=Topic%2C1%7CDevelopment%23DEV%23%7COfficial%20Development%20Assistance%20%28ODA%29%23DEV_ODA%23&pg=0&fc=Topic&bp=true&snb=26&df[ds]=dsDisseminateFinalCloud&df[id]=DSD_CRS%40DF_CRS&df[ag]=OECD.DCD.FSD&df[vs]=1.3&dq=DAC..1000.100._T._T.D.Q._T..&lom=LASTNPERIODS&lo=5&to[TIME_PERIOD]=false).
  - Cleans, merges, and consolidates project details by creating a unified text field (`raw_text`).
  - Produces both a full dataset (`DataPB.csv`) and a beta-test subsample (`DataPBsample.csv`).

### 2. Classification and Estimation (Python Scripts)
- **Classification Pipeline:**  
  - **Classify.py, Relevance_classifier.py, multi-classifier.py:**  
    Utilize transformer-based models (e.g., ClimateBERT) to:
      - Filter projects by relevance (binary classification).
      - Assign detailed climate finance categories (multiclass classification).
  - **EstimationClimateFinance.py:**  
    Applies time series forecasting (e.g., SARIMA) to estimate future climate finance flows based on historical disbursement and commitment data.
  - **Meta-Categorization Script:**  
    - Processes the output from UploadBase.R (e.g., `ClassifiedCRS.csv`).
    - Assigns high-level meta-categories (Adaptation, Mitigation, Environment) based on detailed classification numbers.
    - Performs data integrity checks and saves the final cleaned dataset (`climate_finance_total.csv`).

### 3. Graphical Outputs (Graphs Folder)
This folder contains all publication-ready figures generated by the pipeline, including:
- **Climate Finance Forecast (SARIMA)**
- **Combined Adaptation & Mitigation Plots**
- **Combined Climate Finance Analysis**
- **Donor-Level Comparison Graphs (Combined & Ratio)**
- **Stacked Area and Stackplot Figures for Disbursements and Commitments**

Each graph is accompanied by detailed descriptions in its own README to explain its content and academic relevance.

### 4. Data Sources and Resources
The project relies on several data sources:
- **Raw Project Data:**  
  Clustering outputs from OECD CRS aid activities (e.g., `Data.csv`).
- **Preprocessed Training Data:**  
  A balanced dataset (`train_set.csv`) for model training, derived via extensive filtering and sampling.
- **Auxiliary Files:**  
  JSON label dictionaries (e.g., `reverse_dictionary_classes.json`) for mapping classifier outputs to human-readable labels.
- **Model Weights:**  
  Saved weights for the relevance and multiclass classifiers (`saved_weights_relevance.pt` and `saved_weights_multiclass.pt`).

> **Note:** Due to file size, key datasets and model weights are hosted externally. You can access these files at:  
> [https://drive.uca.fr/d/6058b184ba134a02a708/](https://drive.uca.fr/d/6058b184ba134a02a708/)

### 5. Econometrics Analysis of Aid Determinants (R)

This section of the project focuses on the econometric analysis of the determinants of bilateral aid flows, with a special emphasis on climate finance. The analysis is performed using R and leverages advanced regression techniques to account for both the decision to allocate aid and the magnitude of the allocation.

#### Key Components

- **Data Preparation and Variable Transformation:**  
  The analysis begins with cleaned datasets derived from earlier preprocessing steps. The script further refines these datasets by:
  - Grouping the data by key identifiers such as Year, ProviderISO, and RecipientISO.
  - Aggregating commitment values (converted to USD using appropriate multipliers) and applying logarithmic transformations to variables (e.g., GDP, population, fiscal indicators, and distance).
  - Normalizing or scaling financial and demographic variables to ensure comparability.
  - Creating dummy variables for categorical predictors (e.g., ProviderISO, RecipientISO, Year) using the `fastDummies` package.

- **Double Hurdle Regression Models:**  
  To address the dual decision process in aid allocation—first, the decision whether to commit finance (selection) and second, the decision on the amount of finance committed (intensity)—the analysis employs double hurdle models using the `mhurdle` package. Two models are estimated:
  - **Uncorrelated Model:**  
    This model estimates the two stages (selection and intensity) separately without assuming any correlation between them.
  - **Correlated Model:**  
    This model is updated from the uncorrelated version to allow for correlation between the selection and intensity processes, thus better capturing the underlying decision-making dynamics.

- **Model Comparison and Output Generation:**  
  After fitting the models:
  - Coefficient estimates, standard errors, and significance levels are extracted from both the uncorrelated and correlated models.
  - Key goodness-of-fit metrics such as log-likelihood, McFadden’s pseudo-R², and the coefficient of determination are calculated.
  - The coefficients from both models are merged into combined data frames for side-by-side comparison.
  - Publication-ready regression tables are generated using the `texreg` package, and both CSV and text outputs are saved to the `./Results/` directory.

- **Separate Analyses for Adaptation and Mitigation:**  
  The econometric analysis is performed separately for different facets of climate finance (e.g., adaptation vs. mitigation). For each, the script:
  - Prepares datasets specific to the category (e.g., Rio markers data vs. ClimateFinanceBERT data).
  - Estimates regression models and compares the results.
  - Exports detailed regression results, allowing for the assessment of differences between the classification methods and understanding of the underlying determinants.
---

## How to Replicate

### Data Preparation (R Scripts)
1. **Download Raw Data:**  
   Obtain the raw OECD CRS [text files from the OECD website](https://data-explorer.oecd.org/vis?fs[0]=Topic%2C1%7CDevelopment%23DEV%23%7COfficial%20Development%20Assistance%20%28ODA%29%23DEV_ODA%23&pg=0&fc=Topic&bp=true&snb=26&df[ds]=dsDisseminateFinalCloud&df[id]=DSD_CRS%40DF_CRS&df[ag]=OECD.DCD.FSD&df[vs]=1.3&dq=DAC..1000.100._T._T.D.Q._T..&lom=LASTNPERIODS&lo=5&to[TIME_PERIOD]=false) and place them in the appropriate folders (e.g., `./Data/CRS/`).
2. **Run Treatment.R:**  
   This script will load (by calling `UploadBase.R`), clean, and merge the raw data to produce `DataPB.csv` and `DataPBsample.csv`.

### Model Training and Estimation
1. **Ensure Dependencies:**  
   - For Python scripts, install required packages:
     ```bash
     def install_requirements():
         import subprocess, sys
         try:
             subprocess.check_call([sys.executable, "-m", "pip", "install", "-r", '/UNDERCANOPY/Climate finance estimation/requirements.txt'])
             print("Requirements installed successfully.")
         except subprocess.CalledProcessError as e:
             print("Failed to install requirements:", e)

     install_requirements()
     ```
2. **Download Pre-trained Weights, project clusters and Label Files:**  
   The model weights (`saved_weights_relevance.pt` and `saved_weights_multiclass.pt`) and JSON label mapping files (e.g., `reverse_dictionary_classes.json`) are available from the external drive:
   [https://drive.uca.fr/d/6058b184ba134a02a708/](https://drive.uca.fr/d/6058b184ba134a02a708/)

   > **Please download all the files and arrange them in required folder according to the directory map under**
4. **Run Python Pipelines:**  
   - Execute `EstimationClimateFinance.py` (if applicable) to perform financial estimation and forecasting.
   - Execute `Relevance_classifier.py` or related scripts to fine-tune and evaluate the models.
   - Execute `Multi-classifier.py` or related scripts to fine-tune and evaluate the models.
   - Execute `Classify.py` to run the classification pipeline.
   - Execute `Meta.py` to run the meta categories pipeline.
   - Execute `Graph_Final.py` to run the classification pipeline.

### Econometric analysis (R) 

You will find the related data and script in the `Econometrics` Folder.

---

# Repository organization

When downloaded you repository should have the same organization: 

---
```text
├── Climate finance estimation
│   ├── Data
│   │   ├── Data.csv
│   │   ├── DataPB.csv
│   │   ├── dictionary_classes.json
│   │   ├── projects_clusters.csv
│   │   ├── readme.md
│   │   ├── reverse_dictionary_classes.json
│   │   └── train_set.csv
│   ├── Figures
│   │   ├── graph_final.py
│   │   ├── Graphs
│   │   │   ├── climate_finance_forecast_sarima.png
│   │   │   ├── combined_adaptation_mitigation_plot.png
│   │   │   ├── combined_climate_finance_analysis.png
│   │   │   ├── combined_comparison_by_donor.png
│   │   │   ├── ratio_comparison_by_donor.png
│   │   │   ├── readme.md
│   │   │   ├── stacked_area_adaptation_commitment.png
│   │   │   ├── stacked_area_adaptation_disbursement.png
│   │   │   ├── stacked_area_mitigation_commitment.png
│   │   │   ├── stacked_area_mitigation_disbursement.png
│   │   │   ├── stackplot_commitment.png
│   │   │   └── stackplot_disbursement.png
│   │   └── readme.md
│   ├── Raw Data
│   │   ├── Adaptation with gravity vars amended FULL Feb 14 2023.csv
│   │   ├── CRS
│   │   │   ├── CRS 1973-94 data.txt
│   │   │   ├── CRS 1995-99 data.txt
│   │   │   ├── CRS 2000-01 data.txt
│   │   │   ├── CRS 2002-03 data.txt
│   │   │   ├── CRS 2004-05 data.txt
│   │   │   ├── CRS 2006 data.txt
│   │   │   ├── CRS 2007 data.txt
│   │   │   ├── CRS 2008 data.txt
│   │   │   ├── CRS 2009 data.txt
│   │   │   ├── CRS 2010 data.txt
│   │   │   ├── CRS 2011 data.txt
│   │   │   ├── CRS 2012 data.txt
│   │   │   ├── CRS 2013 data.txt
│   │   │   ├── CRS 2014 data.txt
│   │   │   ├── CRS 2015 data.txt
│   │   │   ├── CRS 2016 data.txt
│   │   │   ├── CRS 2017 data.txt
│   │   │   ├── CRS 2018 data.txt
│   │   │   ├── CRS 2019 data.txt
│   │   │   ├── CRS 2020 data.txt
│   │   │   ├── CRS 2021 data.txt
│   │   │   ├── CRS 2022 data.txt
│   │   │   └── CRS 2023 data.txt
│   │   ├── Mitigation with gravity vars amended FULL Feb 14 2023.csv
│   │   ├── readme.md
│   │   ├── Treatment.R
│   │   └── UploadBase.R
│   ├── readme.md
│   ├── requirements.txt
│   └── Training and Classifying
│       ├── Classify.py
│       ├── EstimationClimateFinance.py
│       ├── meta.py
│       ├── multi-classifier.py
│       ├── readme.md
│       └── Relevance_classifier.py
├── Econometrics
│   ├── Data
│   │   ├── Readme.md
│   │   ├── reg1_mitigation.csv
│   │   ├── reg1.csv
│   │   ├── reg2_mitigation.csv
│   │   ├── reg2.csv
│   │   ├── reg3_mitigation.csv
│   │   └── reg3.csv
│   ├── Estimations.R
│   ├── readme.md
│   └── Results
│       ├── adaptation
│       │   ├── combined_regression_results.csv
│       │   ├── combined_regression_results2.csv
│       │   └── combined_regression_results3.csv
│       ├── Baseline Result for  Mitigation 103950 obs
│       ├── Baseline Result for Adaptation
│       ├── ClimateFinanceBERT Result for Adaptation
│       ├── ClimateFinanceBERT Result for Mitigation
│       ├── mitigation
│       │   ├── combined_regression_results.csv
│       │   ├── combined_regression_results2_mitigation.csv
│       │   └── combined_regression_results3_mitigation.csv
│       ├── Rio Result for Adaptation
│       ├── Rio Result for Mitigation
│       └── summary_sample.csv
├── LICENSE
├── README.md
