# FinAvert: Credit Card Fraud Detection

FinAvert is a machine learning portfolio project focused on detecting fraudulent credit card transactions using Python. It explores data quality, exploratory data analysis, classification under severe class imbalance, model evaluation, and feature importance.

**Project status:** Analysis and model comparison completed; further validation and improvements planned.

## Project Objectives

- Analyze an imbalanced credit card transaction dataset.
- Inspect data quality and assess the effect of duplicate removal.
- Train and compare Logistic Regression and Random Forest classifiers.
- Evaluate models using fraud precision, recall, F1-score, Average Precision, and ROC-AUC.
- Select classification thresholds using validation data.
- Examine Random Forest feature importance.

## Dataset

This project uses a publicly available credit card fraud detection dataset containing 284,807 transactions and 30 input features.

### Initial Dataset

| Property | Value |
|---|---:|
| Original transactions | 284,807 |
| Input features | 30 |
| Target variable | `Class` |
| Legitimate transactions | 284,315 |
| Fraudulent transactions | 492 |
| Missing values | None detected |

The target variable `Class` uses `0` for legitimate transactions and `1` for fraudulent transactions. Fraudulent transactions represent approximately 0.173% of the original dataset.

### Duplicate Handling

A total of 1,081 exact duplicate rows were identified and removed before modeling: 1,062 legitimate records and 19 fraud-labeled records.

The resulting modeling dataset contains 283,726 transactions:

- Legitimate: 283,253
- Fraudulent: 473
- Fraud prevalence: approximately 0.167%

Duplicate removal is a modeling decision rather than an unquestionable data-cleaning rule. Identical transaction records may represent genuine repeated transactions, so the potential effect of removing them is acknowledged.

### Feature Overview

The dataset contains `Time`, `Amount`, anonymized features `V1` through `V28`, and the target variable `Class`.

- `Time` represents elapsed seconds from the first transaction in the dataset.
- `Amount` represents the transaction amount.
- `V1`–`V28` are anonymized features derived through Principal Component Analysis (PCA); their original financial meanings are not directly available.

## Methodology

The analysis follows these stages:

1. Load the dataset and inspect its structure and data quality.
2. Analyze class imbalance, transaction amounts, transaction time, and feature correlations.
3. Identify and remove exact duplicate rows for the modeling dataset.
4. Split the data into training, validation, and test sets using stratification.
5. Train a Random Forest classifier and select its classification threshold using validation data.
6. Train a scaled Logistic Regression baseline.
7. Compare model performance and evaluate the selected thresholds on the held-out test set.
8. Analyze Random Forest feature importance.

The dataset was split into training (60%), validation (20%), and test (20%) subsets. Stratification was used to preserve the class distribution across subsets.

The Random Forest threshold was selected using validation F1-score. Logistic Regression used its own validation-selected threshold. The test set was reserved for final evaluation rather than threshold tuning.

## Model Comparison

The following results were obtained on the held-out test set.

| Metric | Logistic Regression | Random Forest |
|---|---:|---:|
| Classification threshold | 1.0000* | 0.2500 |
| Precision (Fraud) | 90.24% | 90.36% |
| Recall (Fraud) | 77.89% | 78.95% |
| F1-score (Fraud) | 0.8362 | 0.8427 |
| Average Precision | 0.7527 | 0.8522 |
| ROC-AUC | 0.9879 | 0.9557 |
| True positives | 74 | 75 |
| False positives | 8 | 8 |
| False negatives | 21 | 20 |

*The Logistic Regression threshold is displayed rounded to four decimal places. Its selected threshold is near 1.0, so the precise value and threshold-selection procedure should be inspected in the notebook.*

### Random Forest Test Confusion Matrix

| Actual / Predicted | Legitimate | Fraud |
|---|---:|---:|
| Legitimate | 56,643 | 8 |
| Fraud | 20 | 75 |

### Interpretation

Random Forest was selected as the preferred model under the project's classification and precision-recall evaluation criteria. It detected 75 of the 95 fraudulent transactions in the test set, with 8 false-positive alerts.

Logistic Regression achieved a higher ROC-AUC, indicating stronger overall ranking performance under that metric. However, Random Forest achieved higher F1-score, recall, and Average Precision at the selected operating thresholds.

These results illustrate why fraud detection should not be assessed using accuracy or a single evaluation metric alone. The relative costs of missed fraud and false-positive alerts would also influence threshold selection in a real-world application.

## Feature Importance

The Random Forest model assigned its highest impurity-based feature importance scores to:

| Feature | Importance |
|---|---:|
| V14 | 0.1729 |
| V4 | 0.1255 |
| V10 | 0.1053 |
| V12 | 0.1047 |
| V17 | 0.0817 |

The four most important features collectively account for approximately 50.84% of the model's total impurity-based feature importance.

Because the `V1`–`V28` variables are anonymized PCA-derived features, their original financial interpretations cannot be inferred directly from these rankings. Feature importance indicates a model-specific contribution to impurity reduction; it does not establish causation.

## Tools and Technologies

- Python
- Pandas and NumPy
- Matplotlib and Seaborn
- Scikit-learn
- Jupyter Notebook
- Git and GitHub

## Project Structure

```text
FinAvert/
├── data/
│   └── raw/
│       └── processed/
│           └── creditcard.csv
├── notebooks/
│   └── fraud_detection_analysis.ipynb
├── .gitignore
├── LICENSE
├── README.md
└── requirements.txt
```

The dataset is expected at `data/raw/processed/creditcard.csv`. The CSV may be excluded from Git tracking; obtain it from the original dataset provider and place it at the expected path before running the notebook.

## How to Run

### 1. Clone the repository

```bash
git clone https://github.com/jonathan-galamay/FinAvert.git
cd FinAvert
```

### 2. Create and activate a virtual environment

Windows PowerShell:

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Obtain the dataset

Download the credit card fraud dataset from its original public source and place `creditcard.csv` at:

```text
data/raw/processed/creditcard.csv
```

### 5. Run the notebook

Open the project in VS Code or Jupyter and run:

```text
notebooks/fraud_detection_analysis.ipynb
```

Run the notebook cells in order.

## Limitations and Future Improvements

- The dataset is highly imbalanced, and its historical transaction distribution may not represent current fraud patterns.
- Removing exact duplicates may remove legitimate repeated transactions.
- Random stratified splitting does not fully test generalization to future transactions.
- Anonymized PCA-derived features limit direct interpretation.
- The Logistic Regression threshold is very close to 1.0, making the exact threshold and its stability worth reviewing.
- Model performance alone does not establish suitability for production deployment.

Potential improvements include probability calibration, cost-sensitive threshold selection, permutation importance or SHAP analysis, time-based validation, and evaluation on newer transaction data.

## Disclaimer

This project is for educational and portfolio purposes. The results are experimental and should not be interpreted as evidence that either model is ready for deployment in a production financial system.

