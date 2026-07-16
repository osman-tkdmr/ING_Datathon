<div align="center">

# Customer Churn Prediction — ING Hubs Türkiye Datathon

**An end-to-end machine learning pipeline that predicts customer churn from banking transaction history**

[![Python](https://img.shields.io/badge/Python-3.12-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![uv](https://img.shields.io/badge/dependency%20manager-uv-DE5FE9)](https://github.com/astral-sh/uv)
[![LightGBM](https://img.shields.io/badge/model-LightGBM-02569B)](https://lightgbm.readthedocs.io/)
[![XGBoost](https://img.shields.io/badge/baseline-XGBoost-EB0028)](https://xgboost.readthedocs.io/)
[![Optuna](https://img.shields.io/badge/tuning-Optuna-2FA7D6)](https://optuna.org/)
[![SHAP](https://img.shields.io/badge/explainability-SHAP-8A2BE2)](https://shap.readthedocs.io/)

</div>

---

## What this project is

A full ML workflow built for the **ING Hubs Türkiye Datathon** on Kaggle: given a customer's profile and up to two years of monthly transaction behavior, predict whether they will churn. It covers the complete lifecycle — data ingestion, feature engineering, model training and tuning, evaluation against a competition-specific business metric, and explainability — not just a single notebook that fits a model.

## Highlights

- **Caught and fixed a critical temporal data leakage bug.** The original feature engineering aggregated a customer's *entire* transaction history regardless of the prediction date, meaning early-window training rows were built using up to two years of data from the future. Diagnosed via the raw date ranges, root-caused, and fixed by re-deriving every feature from only the data available as of each customer's reference date — the kind of bug that quietly inflates offline metrics and quietly wrecks production performance if it ships.
- **Rebuilt the feature engineering pipeline for correctness *and* speed**, replacing a per-customer Python loop with a vectorized pandas implementation — verified behaviorally identical to the original across 1,500+ synthetic edge cases (missing channels, single-month customers, out-of-order records) before it ever touched real data, and ~3.3x faster at scale.
- **Removed silent leakage in cross-validation** (a scaler fit on the full dataset before splitting) and **eliminated redundant code duplication** by consolidating the competition's custom evaluation metric into a single shared module used by every notebook.
- **Built for reproducibility, not just a one-off run**: persisted Optuna hyperparameter search history to disk (survives kernel restarts), persisted trained models with `joblib`, and made environment setup (Kaggle credentials, GPU/CPU device selection) portable across machines instead of hardcoded.
- **Added model explainability with SHAP**, surfacing which behavioral signals (transaction recency, digital channel usage, trend in activity) drive the model's churn predictions — the kind of output a retention/CRM team can actually act on.

## Results

| Metric | Score |
| --- | --- |
| 5-Fold Cross-Validated Composite Score¹ | **1.15 ± 0.02** |
| Recall @ Top 10% Riskiest Customers | **32.1%** |
| Lift @ Top 10% Riskiest Customers | **3.21x** |

¹ A custom weighted metric (40% Gini, 30% Recall@10%, 30% Lift@10%) defined by the competition, scored relative to its own baseline model.

## Pipeline

```
Raw data (Kaggle API)
        │
        ▼
Feature engineering  →  per-customer transaction aggregates, time-bounded to each prediction date
        │
        ▼
Preprocessing  →  missing-value handling, rare-category grouping, one-hot encoding
        │
        ▼
Model training  →  LightGBM + Optuna hyperparameter search (5-fold CV, early stopping)
        │
        ▼
Evaluation  →  custom Gini / Recall@10% / Lift@10% composite metric
        │
        ▼
Explainability  →  SHAP feature attribution
        │
        ▼
Submission
```

## Repository structure

```text
ING_Datathon/
├── preprocessing.ipynb     # Feature engineering, cleaning, train/test export
├── analysis.ipynb          # Exploratory data analysis
├── baseline_model.ipynb    # XGBoost baseline
├── model.ipynb             # Main LightGBM model, tuning, evaluation, SHAP
├── metrics.py              # Shared competition metric implementation
├── pyproject.toml          # Dependencies
└── uv.lock                 # Locked dependency versions
```

## Technologies

`Python` · `pandas` · `NumPy` · `scikit-learn` · `LightGBM` · `XGBoost` · `CatBoost` · `Optuna` · `SHAP` · `imbalanced-learn` · `matplotlib` / `seaborn` / `plotly` · `uv` · `Kaggle API`

## Getting started

```bash
git clone https://github.com/osman-tkdmr/ING_Datathon.git
cd ING_Datathon
uv sync
```

Run the notebooks in order: `preprocessing.ipynb` → `model.ipynb` (or `baseline_model.ipynb` for the simpler benchmark). Kaggle API credentials are picked up automatically from `~/.config/kaggle/kaggle.json` (override with the `KAGGLE_CONFIG_DIR` environment variable).

## Skills demonstrated

- **Data leakage detection & correction** — identifying temporal and cross-validation leakage that inflates offline metrics, and fixing it at the root cause rather than patching symptoms
- **Feature engineering at scale** — vectorized pandas transformations validated for correctness before deployment
- **Hyperparameter optimization** — Optuna-driven search against a custom, business-defined objective
- **Model evaluation design** — working with non-standard, business-aligned metrics rather than defaulting to generic accuracy/AUC
- **Explainable AI** — SHAP-based model interpretation for stakeholder-facing insights
- **Reproducible ML engineering practices** — persisted experiment tracking, model artifacts, and portable environment configuration
