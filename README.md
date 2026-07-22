# PETR4 Time-Series Forecasting

[![Python](https://img.shields.io/badge/Python-3.12-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![uv](https://img.shields.io/badge/dependencies-uv-DE5FE9?style=flat)](https://docs.astral.sh/uv/)
[![statsmodels](https://img.shields.io/badge/model-ARIMA-4051B5?style=flat)](https://www.statsmodels.org/)
[![Data](https://img.shields.io/badge/data-Yahoo%20Finance-6001D2?style=flat)](https://finance.yahoo.com/quote/PETR4.SA/)
[![Status](https://img.shields.io/badge/status-complete-2EA44F?style=flat)](https://github.com/Giovannipersio/petr4-time-series-forecasting)

An end-to-end time-series project that collects five years of daily PETR4.SA
market data, explores its behavior, engineers stationary features, selects an
ARIMA model, and produces short-term closing-price forecasts.

## Project Objective

The objective is to forecast PETR4 closing prices from historical market data
while maintaining a leakage-free chronological evaluation. The model is fitted
to first differences and its predictions are reconstructed on the original
price scale.

## Key Results

Results below refer to the snapshot collected on **July 21, 2026**.

| Item | Result |
| --- | ---: |
| Period | 2021-07-21 to 2026-07-21 |
| Raw observations | 1,248 |
| Processed observations | 1,239 |
| Selected model | ARIMA(0, 0, 1) on `Close_diff` |
| Validation RMSE | 0.4954 |
| Test RMSE — daily differences | 0.7039 |
| Test directional accuracy | 61.29% |
| Test MAE — reconstructed price | 8.6623 |
| Naive baseline MAE | 8.9595 |

The closing-price level is non-stationary according to the ADF test
(`p = 0.2245`), while its first difference is stationary (`p < 0.0001`). The
selected ARIMA model slightly outperformed a last-price random-walk baseline,
but the practical improvement was modest.

## Workflow

```mermaid
flowchart LR
    YF["Yahoo Finance"] --> N1["01 Data collection"]
    N1 --> N2["02 EDA"]
    N2 --> N3["03 Feature engineering"]
    N3 --> N4["04 ARIMA modeling"]
    N4 --> N5["05 Evaluation and forecasting"]
```

| Notebook | Responsibility | Main output |
| --- | --- | --- |
| [01 — Data collection](notebooks/01_data_collection.ipynb) | Download and validate PETR4.SA data | `petr4_daily.csv` |
| [02 — Exploratory analysis](notebooks/02_eda.ipynb) | Analyze prices, returns, volume, volatility, correlation, and decomposition | Visual diagnostics |
| [03 — Feature engineering](notebooks/03_feature_engineering.ipynb) | Create returns, rolling statistics, and first differences | `petr4_features.csv` |
| [04 — Modeling](notebooks/04_modeling.ipynb) | Split chronologically, search ARIMA orders, and fit the selected model | Model and search metadata |
| [05 — Forecasting](notebooks/05_forecasting.ipynb) | Evaluate the test period, compare the baseline, and forecast ten sessions | Forecast table and final model |

## Repository Structure

```text
petr4-time-series-forecasting/
|-- data/
|   |-- raw/                  # Yahoo Finance snapshot
|   `-- processed/            # Features and forecasts
|-- models/                   # Fitted ARIMA objects and metadata
|-- notebooks/                # End-to-end analytical workflow
|-- src/                      # Package scaffold
|-- pyproject.toml
|-- uv.lock
`-- README.md
```

## Reproduce the Project

Requirements: Git and [uv](https://docs.astral.sh/uv/).

```bash
git clone https://github.com/Giovannipersio/petr4-time-series-forecasting.git
cd petr4-time-series-forecasting
uv sync --locked
```

Open the notebooks in VS Code or another Jupyter-compatible editor, select the
interpreter from `.venv`, and run them in numerical order. On Windows, the
interpreter is `.venv/Scripts/python.exe`; on Linux and macOS, it is
`.venv/bin/python`.

## Methodological Notes

- The 70/15/15 train, validation, and test split preserves temporal order.
- ARIMA orders are selected on validation MSE; the test period remains isolated.
- Statsmodels receives a `RangeIndex`, while real dates are restored manually on forecasts.
- Future dates approximate business days and do not account for B3 holidays.
- The five-year Yahoo Finance window and all reported results change when the pipeline is rerun.

## Main Dependencies

`pandas`, `numpy`, `yfinance`, `statsmodels`, `scikit-learn`, `matplotlib`, and
`seaborn`. Versions are declared in `pyproject.toml` and locked in `uv.lock`.

## Responsible Interpretation

ARIMA provides a transparent statistical baseline but does not incorporate
news, macroeconomic variables, oil prices, exchange rates, or volatility
models. Prediction intervals widen quickly and abrupt market movements remain
difficult to anticipate. Results are analytical estimates, not investment
advice.

## Authors

Portfolio revision and repository maintenance by **Giovanni Persio**.

The original academic project was developed with **Rodrigo Corrêa Fardin** and
**Marcelo Botelho** as part of a data science specialization course.
