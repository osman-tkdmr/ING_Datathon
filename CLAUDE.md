# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Notebook-based ML pipeline for the ING Hubs Türkiye Datathon Kaggle competition: binary churn
classification (`churn` column) from customer profile + monthly transaction history data. There is
no source package — all logic (feature engineering, custom metrics, training) lives inline in four
Jupyter notebooks. Code comments/docstrings inside the notebooks are written in Turkish.

## Environment & Commands

- Python 3.12, dependency management via `uv` (`uv.lock` is the source of truth).
- Install: `uv sync`
- Run notebooks: `uv run jupyter lab` (or execute cells directly in an IDE with the `.venv` kernel).
- There are no lint configs, test suites, or CI in this repo — validation happens by re-running
  notebook cells and inspecting printed metrics/plots.
- Notebooks call the Kaggle API directly (`kaggle`, `kagglehub.competition.competition_download`)
  and read credentials from a hardcoded path: `/home/osman/.config/kaggle/kaggle.json`. This path
  is machine-specific — expect it to need editing (or a `KAGGLE_CONFIG_DIR` env var) on another
  machine before a notebook can execute its first cell.

## Notebook Pipeline & Execution Order

The four notebooks form a pipeline, but **only `preprocessing.ipynb` → `model.ipynb` is a real
dependency chain**; `baseline_model.ipynb` and `analysis.ipynb` independently re-download and
re-merge raw data instead of consuming preprocessing's output.

1. **`preprocessing.ipynb`** — downloads the Kaggle competition dataset, engineers per-customer
   features from `customer_history.csv` (via `create_customer_features`), merges with
   `customers.csv` + `referance_data(.csv/_test.csv)`, one-hot encodes categoricals, drops
   ID/date columns, and writes `train_data.csv` / `test_data.csv` / `sample_submission.csv` to the
   repo root (git-ignored). **Must be run before `model.ipynb`.**
2. **`analysis.ipynb`** — standalone EDA (churn distribution, missing values, categorical/numeric
   distributions, correlations, time-based trends). Does not feed the other notebooks.
3. **`baseline_model.ipynb`** — standalone XGBoost baseline. Re-downloads raw data itself, merges
   `customers` + `referance_data` directly (no transaction-history features), one-hot encodes +
   min-max scales, tunes with Optuna, and submits via `kaggle.api.competition_submit`.
4. **`model.ipynb`** — main LightGBM workflow. Reads `train_data.csv`/`test_data.csv` produced by
   `preprocessing.ipynb`, min-max scales, tunes hyperparameters with Optuna (`n_trials=50`)
   against the custom composite metric, trains a 5-fold CV ensemble, and submits predictions.

Since raw CSVs and generated `train_data.csv`/`test_data.csv` are git-ignored, always check whether
they exist locally before assuming a notebook can run standalone — `preprocessing.ipynb`'s Kaggle
download step is the actual data entry point.

## Custom Evaluation Metric (duplicated in both model notebooks)

`baseline_model.ipynb` and `model.ipynb` each define their own copy of the same metric functions
(`recall_at_k`, `lift_at_k`, `convert_auc_to_gini`, `ing_hubs_datathon_metric`) rather than sharing
code. If you change the metric definition, update it in **both** places.

`ing_hubs_datathon_metric` is the objective Optuna optimizes (not raw AUC): it computes Gini,
Recall@10%, and Lift@10%, divides each by a hardcoded baseline model's score for that metric
(`roc_auc=0.6925726757936908`, `recall_at_10perc=0.18469015795868773`,
`lift_at_10perc=1.847159286784029`), then combines the ratios with weights 40% Gini / 30%
Recall@10% / 30% Lift@10%. This baseline is a fixed external reference point (the competition's
benchmark model), not something recomputed from the data — don't refactor it into something derived
from the current dataset.

## Feature Engineering Conventions

- `create_customer_features` (in `preprocessing.ipynb`) aggregates `customer_history.csv` per
  `cust_id` into mean/std/min/max/last/trend (linear fit slope) features for mobile EFT, credit
  card transactions, and active product category, plus channel-usage flags and month-over-month
  deltas.
- Missing-value sentinel convention: channels with **no usage history at all** for a customer are
  filled with `-2` (distinct from real zero-usage months); any remaining NaNs after feature
  aggregation are filled with `-1`. Preserve this distinction when adding features — don't collapse
  both cases to a single fill value.
- `work_sector` nulls are backfilled from `work_type`; `cust_age_month` is derived as
  `age * 12 - tenure`.
- Categorical encoding is one-hot via `pd.get_dummies(..., drop_first=True)`; ID/date columns
  (`cust_id`, `ref_date`, `first_activity_month`, `last_activity_month`) are dropped before
  modeling, not used as features.

## Models

- `model.ipynb` (primary): LightGBM (`lgb.LGBMClassifier`), Optuna-tuned, `StratifiedKFold(n_splits=5)`,
  predictions averaged across the 5 fold models (bagging ensemble, not a single refit).
- `baseline_model.ipynb`: XGBoost (`xgb.XGBClassifier`) with GPU device + early stopping, single
  train/test split for tuning then refit on full data.
- CatBoost is a declared dependency in `pyproject.toml` but has no implementation in any notebook.
- `random_state=42` is used throughout for reproducibility (splits, CV folds, model seeds).
