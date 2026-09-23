# Electricity Demand Forecasting

Hourly electricity demand forecasting using historical demand, weather, and calendar
features, modeled with XGBoost.

## Overview

This project builds a time-series regression model to forecast hourly electricity
demand (in MW) from historical demand patterns, temperature, humidity, and calendar
signals (hour of day, day of week, month, weekend flag, etc.). The final model is an
XGBoost regressor trained on ~5 years of hourly data, evaluated on a full year of
held-out data.

## Repository Contents

| File | Description |
|---|---|
| `Electricity_Demand_Forecasting_Project.ipynb` | Main analysis notebook — data cleaning, feature engineering, EDA, modeling, and evaluation |
| `Electricity_Demand_Forecasting_Project.html` | Static HTML export of the notebook (view without running Jupyter) |
| `electricity_demand_dataset.csv` | Hourly dataset: timestamp, calendar fields, temperature, humidity, and demand |
| `electicity_xgb_prediction_model.pkl` | Trained XGBoost model, saved with `joblib` |

## Dataset

- **Granularity:** Hourly
- **Span:** January 2020 – December 2024 (~43,800 hourly records)
- **Columns:** `Timestamp`, `hour`, `dayofweek`, `month`, `year`, `dayofyear`,
  `Temperature` (°C), `Humidity` (%), `Demand` (MW)

## Data Cleaning & Preprocessing

- Parsed `Timestamp` to datetime and set it as the DataFrame index
- Dropped fully-empty rows
- Forward-filled calendar fields (`hour`, `dayofweek`, `month`, `year`, `dayofyear`)
- Backward-filled weather fields (`Temperature`, `Humidity`)
- Time-interpolated the `Demand` target for any remaining gaps
- Cast calendar fields to integer type

## Feature Engineering

- `quarter` and `weekofyear`, derived from the timestamp index
- `is_weekend` flag (Saturday/Sunday)
- Lag features: `Demand_lag_24hr` (same hour, previous day) and `Demand_lag_168hr`
  (same hour, previous week)
- Rolling statistics: 24-hour rolling mean and rolling standard deviation of demand
- Dropped rows with nulls introduced by the lag/rolling windows

## Exploratory Data Analysis

- Demand over time (full series)
- Demand distribution by hour of day and by month (boxplots)
- Demand vs. temperature scatter plot
- Correlation heatmap across all engineered features

## Modeling

- **Train/test split:** chronological — trained on data through 2023-12-31, tested
  on the full 2024 calendar year (~8,784 hours) to simulate a real forecasting
  scenario rather than a random split
- **Model:** `XGBRegressor` (XGBoost)
  - `n_estimators=1000`, `learning_rate=0.01`, `early_stopping_rounds=50`,
    `objective='reg:squarederror'`, `random_state=42`
  - Trained with an eval set on both train and test partitions for early stopping

## Results

| Metric | Value |
|---|---|
| RMSE | ≈ 175.2 MW |
| MAE | ≈ 123.5 MW |

A plot of actual vs. predicted demand over the 2024 test period is included in the
notebook.

## How to Run

1. Install dependencies:
   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn xgboost holidays joblib
   ```
2. Open `Electricity_Demand_Forecasting_Project.ipynb` in Jupyter and run all cells
   in order (the notebook currently reads the CSV from a local Windows path — update
   the `pd.read_csv(...)` path to point to `electricity_demand_dataset.csv` in this
   folder before running).
3. To reuse the trained model without retraining:
   ```python
   import joblib
   model = joblib.load("electicity_xgb_prediction_model.pkl")
   predictions = model.predict(X_new)
   ```

## Tech Stack

Python · pandas · NumPy · Matplotlib · Seaborn · scikit-learn · XGBoost · joblib

## Possible Improvements

- Incorporate a holiday indicator (the `holidays` package is installed in the
  notebook but not yet used as a feature)
- Hyperparameter tuning (grid/random search or Bayesian optimization)
- Compare against baseline models (e.g., linear regression, SARIMA) to quantify
  XGBoost's improvement more rigorously
- Cross-validate with `TimeSeriesSplit` (imported but not yet used) instead of a
  single chronological split
