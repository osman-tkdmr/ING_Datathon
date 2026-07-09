# ING Datathon Churn Prediction

This repository contains a notebook-based machine learning workflow for customer churn prediction using the ING Hubs Türkiye Datathon dataset. The project combines customer profile data with historical transaction behavior to build and evaluate classification models for predicting churn.

## Features

- Customer churn prediction using tabular machine learning
- Data preprocessing and feature engineering from customer and transaction history
- Handling of missing values and categorical feature encoding
- Exploratory data analysis and visualization
- Baseline model training with XGBoost
- Advanced model experimentation with LightGBM and Optuna hyperparameter tuning
- Custom evaluation metrics such as Gini, Recall@10%, and Lift@10%
- Submission file generation for competition-style prediction outputs

## Tech Stack

### Data Processing

- Python
- pandas
- numpy
- pyarrow
- fastparquet
- dask

### Machine Learning

- scikit-learn
- XGBoost
- LightGBM
- CatBoost
- imbalanced-learn
- Optuna

### Visualization

- matplotlib
- seaborn
- plotly

### Project Tools

- Jupyter notebooks
- uv
- Kaggle API

## Project Structure

```text
ING_Datathon/
├── analysis.ipynb          # Exploratory analysis, data quality checks, and feature investigation
├── baseline_model.ipynb    # Early baseline model experiments and evaluation metrics
├── model.ipynb             # Final modeling workflow with LightGBM and Optuna tuning
├── preprocessing.ipynb     # Data cleaning, feature engineering, and export of training data
├── pyproject.toml          # Python dependency definitions
├── uv.lock                 # Locked dependency versions for reproducible installs
└── README.md               # Project documentation
```

## Architecture

The repository follows a notebook-centric data science workflow rather than a packaged application architecture. The practical structure is:

- Data ingestion from Kaggle competition files
- Data preprocessing and feature engineering in notebooks
- Exploratory analysis and visualization
- Model training and hyperparameter search
- Prediction export for submission

The main workflow is effectively a supervised learning pipeline for binary classification with tabular features derived from customer attributes and transaction history.

## Installation

This project is intended to run in a Python environment with the dependencies listed in pyproject.toml.

1. Clone the repository:

```bash
git clone https://github.com/osman-tkdmr/ING_Datathon.git
cd ING_Datathon
```

2. Create and activate a virtual environment (recommended):

```bash
python3 -m venv .venv
source .venv/bin/activate
```

3. Install dependencies:

```bash
pip install -e .
```

If you use uv, the project can also be installed with:

```bash
uv sync
```

## Usage

The repository is primarily used through the notebooks:

- Start with preprocessing.ipynb to create the engineered training and test datasets.
- Use analysis.ipynb to inspect data distributions, churn patterns, and feature behavior.
- Run baseline_model.ipynb for an initial XGBoost baseline.
- Use model.ipynb for the more advanced LightGBM-based modeling and submission generation.

A typical flow is:

1. Download or place the required data files in the project directory.
2. Run preprocessing.ipynb.
3. Train and evaluate the model in the modeling notebooks.
4. Export predictions to a submission file.

## Configuration

The notebooks expect the following data files to be available in the working directory or accessible through the Kaggle competition download path:

- customer_history.csv
- customers.csv
- referance_data.csv
- referance_data_test.csv
- sample_submission.csv

The notebooks use the Kaggle API to download the dataset through the competition identifier:

```python
from kaggle.api.kaggle_api_extended import KaggleApi
from kaggle.api.kaggle_api_extended import competition_download

path = competition_download('ing-hubs-turkiye-datathon')
```

If the dataset is not available locally, Kaggle credentials must be configured before running the notebooks.

## Main Components

### preprocessing.ipynb

- Purpose: prepares the modeling dataset from raw customer and history files.
- Responsibility: fills missing values, derives customer age-in-months, aggregates transaction history statistics, and exports training/test CSV files.
- Relationship: serves as the input preparation step for the modeling notebooks.

### analysis.ipynb

- Purpose: examines data quality and business understanding of churn behavior.
- Responsibility: profiles the dataset, visualizes churn patterns, analyzes missing values, and explores time-based customer behavior.
- Relationship: supports feature selection and interpretation before model training.

### baseline_model.ipynb

- Purpose: establishes a baseline classifier.
- Responsibility: builds an XGBoost-based model, evaluates it using custom churn metrics, and produces a first submission candidate.
- Relationship: provides a simpler benchmark for comparison with later models.

### model.ipynb

- Purpose: implements the more advanced predictive pipeline.
- Responsibility: trains LightGBM classifiers with Optuna tuning, evaluates them using Gini/Recall/Lift metrics, and creates a final submission artifact.
- Relationship: represents the main modeling stage of the repository.

## Workflow

The end-to-end workflow implemented by the notebooks is:

1. Load customer, transaction history, and reference datasets.
2. Clean and enrich customer records.
3. Aggregate historical transaction behavior into customer-level features.
4. Merge customer features with churn labels and reference dates.
5. Engineer categorical and numeric features for modeling.
6. Train classification models using cross-validation and hyperparameter search.
7. Evaluate models using churn-specific metrics.
8. Generate predictions in a submission-ready format.

## Dependencies

| Library | Purpose |
| --- | --- |
| pandas | Data loading, transformation, and tabular manipulation |
| numpy | Numerical operations and array handling |
| scikit-learn | Preprocessing, train/test splitting, cross-validation, metrics |
| XGBoost | Gradient boosting classifier used in the baseline workflow |
| LightGBM | Gradient boosting classifier used in the main modeling notebook |
| Optuna | Hyperparameter optimization |
| matplotlib | Plotting and figure generation |
| seaborn | Statistical visualizations |
| plotly | Interactive charts |
| pyarrow / fastparquet | Parquet I/O support |
| kaggle | Dataset acquisition from Kaggle |

## Error Handling

The notebooks include basic defensive programming patterns for data processing:

- Missing values are explicitly inspected and filled where appropriate.
- Memory-safe parquet processing is implemented in the analysis notebook for large datasets.
- Checkpointing is used during long-running windowed feature generation to avoid losing progress.
- Exceptions during batch processing are printed and surfaced to the user.

## Performance Notes

The project includes several practical performance-oriented choices:

- Parquet-based data handling for efficient storage and access
- Chunked processing for window-based feature engineering
- Checkpointing during large transformations to support interruption recovery
- Use of tree-based models that scale well on tabular data
- Sampling used in some exploratory visualizations for large datasets

## Future Improvements

Potential enhancements for this repository include:

- Turning the notebooks into a modular Python package or pipeline script
- Adding reproducible training configuration files
- Implementing automated tests for preprocessing and feature generation
- Adding model monitoring and experiment tracking
- Expanding the feature set with temporal sequences and lag-based features
- Packaging the workflow into a CLI or API for repeatable execution

## Screenshots

No screenshots are currently included in the repository.

## License

No license file detected.

## Contributing

Contributions are welcome. A practical workflow would be:

1. Fork the repository.
2. Create a feature branch.
3. Make your changes and keep the notebooks reproducible.
4. Open a pull request with a clear summary of the update.

## Author

Author information is not available in the repository metadata. The project is currently documented as a notebook-based data science experiment rather than a packaged application.
