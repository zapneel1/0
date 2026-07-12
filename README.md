# Loan Recovery Prediction for Asset Reconstruction Company (ARC)

This repository contains my submission for the **Colrows AI/ML Internship Assignment**, focused on predicting the recovery rate of defaulted loans using historical loan and recovery-process data.

## Problem Statement

The objective is to predict the **recovery_rate** (fraction of outstanding principal recovered) for a portfolio of defaulted loans.

The solution includes:

- Data exploration
- Feature engineering
- Target leakage prevention
- CatBoost regression model
- Model evaluation
- Feature importance analysis
- Test set prediction generation

---

## Project Structure

```
.
├── Colrows_Assignment.ipynb      # Complete implementation
├── predictions.csv               # Test set predictions
├── Report.pdf                    # Assignment write-up
└── README.md
```

---

## Approach

### Data Exploration
- Inspected dataset structure
- Identified missing values
- Examined numerical and categorical features

### Feature Engineering
Additional features created include:

- Average FICO score
- FICO score spread
- Loan-to-income ratio
- Revolving balance-to-income ratio

### Leakage Prevention

The following variables were excluded from model training:

- `recoveries`
- `collection_recovery_fee`
- `recovery_rate` (target)
- `payment_plan_status` (not available in the test dataset)

This ensures the model only uses features available during inference.

---

## Model

**CatBoostRegressor**

Reasons for selection:

- Native handling of categorical variables
- Robust handling of missing values
- Strong performance on tabular datasets
- Minimal preprocessing requirements

---

## Evaluation

Validation metrics:

| Metric | Value |
|---------|-------|
| MAE | 0.0950 |
| RMSE | 0.1179 |
| R² | 0.6291 |

---

## Feature Importance

Top contributing features include:

1. recovery_status
2. days_past_due_at_default
3. debt-to-income ratio (DTI)
4. Average FICO score
5. FICO range

---

## Requirements

```
pandas
numpy
matplotlib
scikit-learn
catboost
```

Install using:

```bash
pip install pandas numpy matplotlib scikit-learn catboost
```

---

## Running the Notebook

1. Open the notebook in Google Colab or Jupyter Notebook.
2. Place the provided training and test CSV files in the working directory.
3. Run all cells sequentially.
4. The notebook generates:

- Validation metrics
- Feature importance
- `predictions.csv`

---

## Notes

This solution emphasizes:

- Preventing target leakage
- Explainable feature engineering
- Proper validation methodology
- A production-oriented machine learning workflow
