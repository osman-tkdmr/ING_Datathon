# ING Datathon Churn Prediction

This repository contains a notebook-based machine learning workflow for customer churn prediction using the ING Hubs Türkiye Datathon dataset. The project uses customer profile data and historical transaction behavior to train binary classification models that estimate whether a customer will churn.

## Project Overview

The repository implements a tabular classification pipeline for a churn prediction problem. The notebooks load customer, transaction history, and reference datasets, engineer features, train models, and export predictions. A Kaggle competition dataset is referenced through the Kaggle API, and the target variable is the binary column `churn`.

No deep learning model was detected in the repository.

## Features

The implemented workflow includes:

- Data preprocessing
- Missing value handling
- Feature engineering from transaction history
- Exploratory data analysis and visualization
- Baseline model training
- Cross-validation
- Hyperparameter optimization with Optuna
- Model evaluation with custom churn-oriented metrics
- Prediction export for submission

## Repository Structure

```text
ING_Datathon/
├── analysis.ipynb          # Exploratory analysis and feature investigation
├── baseline_model.ipynb    # Baseline XGBoost experiments and evaluation
├── model.ipynb             # Main LightGBM modeling workflow with Optuna
├── preprocessing.ipynb     # Data cleaning, feature engineering, and CSV export
├── pyproject.toml          # Python dependencies
├── uv.lock                 # Locked dependency versions
└── README.md               # Project documentation
```

No dedicated source package, data directory, or model checkpoint directory was detected in the repository.

## Dataset

The repository references a Kaggle competition dataset named `ing-hubs-turkiye-datathon`.

The notebooks load the following files:

- `customer_history.csv`
- `customers.csv`
- `referance_data.csv`
- `referance_data_test.csv`
- `sample_submission.csv`

Key details detected from the repository:

- Dataset source: Kaggle competition dataset
- Task: binary classification
- Target column: `churn`
- Train/test structure: the repository uses `referance_data` for training labels and `referance_data_test` for test/reference data
- Sample size and feature count: not explicitly documented in repository files

## Exploratory Data Analysis

The analysis notebook performs several exploratory checks and visualizations:

- Churn distribution analysis
- Missing value inspection
- Categorical value distributions for fields such as `gender`, `work_type`, `work_sector`, and `province`
- Numeric feature distribution plots
- Correlation analysis between numeric variables and the target
- Time-based transaction trend analysis
- Investigation of customer history windows and customer-level aggregates

The repository also includes feature engineering based on aggregated transaction history statistics such as minimum, maximum, median, and mean values per customer.

## Data Preprocessing

The preprocessing workflow includes:

- Loading customer, history, and reference datasets
- Filling missing values in `work_sector` using `work_type`
- Creating a derived feature for customer age in months
- Aggregating historical transaction features per customer
- Merging customer-level features with churn labels and reference dates
- Encoding categorical variables with one-hot encoding
- Dropping non-model columns such as identifiers and date fields
- Exporting prepared train and test CSV files

## Models

The following models were detected in the repository:

| Model | Evidence | Purpose |
| --- | --- | --- |
| XGBoost | `baseline_model.ipynb` | Baseline classifier for churn prediction |
| LightGBM | `model.ipynb` | Main model used in the final experiment workflow |
| CatBoost | `pyproject.toml` | Dependency present, but no implementation was found in the notebooks |

## Training Pipeline

The workflow implemented by the notebooks is:

Raw data

↓

Data loading and inspection

↓

Cleaning and missing value handling

↓

Feature engineering from customer history

↓

Dataset preparation for modeling

↓

Model training and validation

↓

Evaluation with churn-specific metrics

↓

Prediction export for submission

## Evaluation

The notebooks use the following evaluation metrics:

- ROC-AUC
- Gini
- Recall@10%
- Lift@10%

The repository contains notebook output showing a reported Gini value of `0.5310` for the LightGBM experiment. The baseline notebook also contains evaluation output, but the repository does not include a single consolidated leaderboard or final submission score file.

## Results

The repository shows a progression from a simpler baseline to a more advanced model selection workflow:

| Experiment | Model | Evidence | Notes |
| --- | --- | --- | --- |
| Baseline | XGBoost | `baseline_model.ipynb` | Early benchmark with custom evaluation metrics |
| Main experiment | LightGBM | `model.ipynb` | Optuna-based tuning and stronger evaluation workflow |

No separate final leaderboard file or exported benchmark summary was detected in the repository.

## Technologies

### Programming Languages

- Python

### Libraries

- pandas
- numpy
- scikit-learn
- pyarrow
- fastparquet
- dask
- imbalanced-learn

### Machine Learning Frameworks

- XGBoost
- LightGBM
- CatBoost

### Visualization

- matplotlib
- seaborn
- plotly

### Development Tools

- Jupyter Notebook
- uv
- Kaggle API

## Installation

The project is intended to run with Python 3.12, as indicated by the repository’s `.python-version` file.

```bash
git clone https://github.com/osman-tkdmr/ING_Datathon.git
cd ING_Datathon
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

If you use `uv`, the project can also be set up with:

```bash
uv sync
```

## Usage

The notebooks are the main entry points:

1. Run `preprocessing.ipynb` to prepare the training and test datasets.
2. Use `analysis.ipynb` to inspect the data and understand churn patterns.
3. Run `baseline_model.ipynb` for a simpler XGBoost baseline.
4. Use `model.ipynb` for the main LightGBM-based training workflow and submission export.

The notebooks assume that the required CSV files are available locally or can be downloaded from Kaggle.

## Reproducibility

The repository contains some reproducibility signals:

- `random_state=42` is used in several train/test and cross-validation steps
- Dependency versions are locked in `uv.lock`
- No dedicated configuration file for training parameters was detected

## Future Improvements

Potential next steps based on the current implementation:

- Convert the notebook workflow into a modular training pipeline
- Add automated tests for preprocessing and feature generation
- Introduce experiment tracking and model registry tools
- Expand feature engineering with lag-based or temporal features
- Add explainability tools such as SHAP
- Package the workflow into a CLI or API for repeatable deployment

## License

No license file detected.

## Acknowledgements

This repository references a Kaggle competition dataset and uses several open-source Python libraries. No additional acknowledgements or third-party citations were detected in the repository files.
