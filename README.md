# OvaRisk-ML

Machine-learning framework for individualized ovarian cancer risk prediction from clinico-genetic data.

This repository accompanies the study *"Artificial intelligence-based prediction of ovarian cancer using clinico-genetic data: a multicentre development and external comparison study."* OvaRisk-ML integrates routinely collected clinical variables and genetic (BRCA1/2) information from a large multicentre Korean cohort to stratify individuals into high- and non-high-risk groups and to estimate personalized ovarian cancer risk. Model predictions are made interpretable with SHAP and are externally benchmarked against the established CanRisk (BOADICEA) model.

- **Interactive web app:** https://pipetapp.com/project/ovarian_risk
- **Status:** Under review

## Overview

Ovarian cancer carries high mortality, and conventional risk assessment relies mainly on genetic factors and scoring models that poorly capture interactions among risk factors. OvaRisk-ML addresses this by combining clinical and genetic data in a machine-learning model that provides individualized risk stratification with real-world clinical applicability.

- **Data source:** Catholic Data Warehouse (CDW) electronic medical records (EMR) from seven hospitals of The Catholic University of Korea, 2011–2021.
- **Cohort:** 9,486 individuals — 6,974 patients diagnosed with ovarian, breast, or pancreatic cancer and 2,512 non-cancer controls.
- **Task:** Binary classification of ovarian cancer, modeled separately for a high-risk and a non-high-risk group.
- **Best model:** CatBoost — accuracy 80.8%, AUROC 82.8% (mean over 30 iterations of 10-fold stratified cross-validation).
- **External comparison:** Strong concordance with CanRisk v2.0 for lifetime risk (overall r = 0.82, 95% CI 0.79–0.85, p < 0.001), including in the absence of BRCA testing data.

## Methods summary

- **Algorithms compared:** Logistic Regression (LR), Random Forest (RF), Gradient Boosting Machine (GBM), and Categorical Boosting (CatBoost); a Multilayer Perceptron (MLP) was evaluated for comparison. CatBoost was selected for all downstream analyses.
- **Risk groups:** *High-risk* = non-cancer individuals with a family history of ovarian/breast/pancreatic cancer, breast cancer before age 40, or pancreatic cancer; *non-high-risk* = all others. Each group is further stratified by whether BRCA1/2 testing was performed.
- **Feature selection:** Clinical/literature-driven selection, multicollinearity removal (VIF > 10), and Recursive Feature Elimination (RFE, 5-fold CV) with clinician review.
- **Validation:** 3:1 stratified train/test split with 10-fold stratified cross-validation on the training set; the full pipeline (split, tuning, evaluation) was replicated 30 times for stability.
- **Hyperparameter tuning:** Bayesian optimization.
- **Class-imbalance calibration:** Bayesian probability calibration adjusting predicted probabilities toward real-world ovarian cancer prevalence.
- **Interpretability:** SHAP (Shapley Additive Explanations), computed per subgroup.
- **External benchmark:** Lifetime risk from age 30 compared against CanRisk v2.0 (BOADICEA) via API, with Pearson correlation and Bland–Altman agreement.

### Key predictors (SHAP)

- **High-risk model:** family history of cancer (ovary/breast/pancreatic), age, menopausal status, BRCA1/2 status, and BMI.
- **Non-high-risk model:** menopausal status, age, pregnancy, personal cancer history, family history of cancer, diabetes mellitus, and BRCA1/2 status.

## Repository structure

| File | Description |
| --- | --- |
| `Dataset.ipynb` | Cohort construction and preprocessing from EMR data (inclusion/exclusion, variable extraction, dataset assembly). |
| `recursive feature selection.ipynb` | Recursive Feature Elimination (RFE) for variable selection. |
| `main.ipynb` | Main modeling pipeline — trains and compares LR, RF, XGBoost/GBM, and CatBoost; performance metrics and statistical comparison (paired t-test). |
| `subgroup analysis.ipynb` | High-risk and non-high-risk subgroup modeling, SHAP interpretability, and per-group performance. |
| `lifetime risk cumulative incidence plot.ipynb` | Lifetime risk estimation, CanRisk external comparison, and cumulative incidence plots. |
| `README.md` | This file. |

## Requirements

The notebooks were developed in Python 3 (Jupyter). Core dependencies:

- `catboost`, `xgboost`, `scikit-learn`, `imbalanced-learn` (imblearn)
- `hyperopt`, `scikit-optimize` (skopt)
- `shap`
- `numpy`, `pandas`, `scipy`
- `matplotlib`, `seaborn`, `tqdm`

Install with:

```bash
pip install catboost xgboost scikit-learn imbalanced-learn hyperopt scikit-optimize shap numpy pandas scipy matplotlib seaborn tqdm
```

## Data availability

The clinical data cannot be shared publicly because they contain sensitive information about human subjects. Data may be available to researchers who meet the criteria for access to confidential data, subject to institutional approval. Requests should be directed to the corresponding author.

## Ethics

The study was approved by the Institutional Review Board of Seoul St. Mary's Hospital (approval numbers KC22WISI0596, KC21TISI1016, KC17TESI0690). The requirement for informed consent was waived given the retrospective design, and all data were de-identified. The study was conducted in accordance with the Declaration of Helsinki.

## Citation

If you use this code, please cite the associated manuscript (citation to be updated upon publication):

> Bak SE, Lee C, Choi S, Jung S, Park H, Lee Y-J, Choi B, Choi YJ. *Artificial intelligence-based prediction of ovarian cancer using clinico-genetic data: a multicentre development and external comparison study.* (Under review.)
