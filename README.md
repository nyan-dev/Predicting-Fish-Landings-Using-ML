> **Context:** This is a standalone ML research project exploring fisheries forecasting in Malaysia. It is related to the thesis domain but is not the main thesis output — the thesis focuses on anomaly detection in healthcare claims.

# Predicting Fish Landings Using Machine Learning

## Overview

This project forecasts **monthly fish landings across Malaysian states**
using historical fisheries data combined with weather variables. The
workflow integrates fisheries statistics with climate indicators, builds
a cleaned dataset, evaluates multiple forecasting models, and generates
forecasts through **2030**.

The project consists of two main notebooks: 1. **Data Cleaning
Notebook** -- prepares and merges fisheries and weather datasets 2.
**Training Notebook** -- performs EDA, builds models, evaluates them,
and generates forecasts

------------------------------------------------------------------------

## Problem Statement

Fish landings are affected by environmental conditions and seasonal
fishing patterns. The goal of this project is to determine whether
**machine learning models can accurately forecast monthly fish
landings** at the state level.

Key research questions:

-   Can fish landings be predicted using historical data?
-   Do lag features improve forecast accuracy?
-   Do weather variables contribute predictive value?
-   Does monsoon seasonality improve forecasting performance?
-   Which model works best: gradient boosting or deep learning?

------------------------------------------------------------------------

## Project Structure

    project-root
    │
    ├── notebooks
    │   ├── data_cleaning.ipynb
    │   └── training.ipynb
    │
    ├── data
    │   ├── fish_landings.csv
    │   └── monthly_weather_data_malaysia.csv
    │
    ├── outputs
    │   ├── processed_data.csv
    │   └── forecasts_2030.csv
    │
    └── README.md

------------------------------------------------------------------------

## Data Sources

### 1. Fish Landings Dataset

Contains monthly fisheries landing records.

Example columns:

-   `date`
-   `state`
-   `coast`
-   `landings`

Coverage:

-   **January 2018 -- December 2023**

------------------------------------------------------------------------

### 2. Weather Dataset

Monthly climate indicators for locations across Malaysia.

Example columns:

-   `temperature`
-   `humidity`
-   `pressure`
-   `dew_point`
-   `wind_speed`
-   `gust`
-   `wind_chill`
-   `uv_index`
-   `visibility`
-   `pollutant_value`

Coverage:

-   Begins in **1996**, with overlapping data used for 2018--2023.

------------------------------------------------------------------------

## Data Cleaning Pipeline

The **data cleaning notebook** performs the following steps.

### 1. Date Processing

-   Convert `date` columns to datetime
-   Extract `year_month` for monthly aggregation

### 2. State Name Standardization

-   Harmonize state names between datasets
-   Example mapping:
    -   `Labuan` → `W.P. Labuan`

### 3. Remove Aggregate Rows

Rows representing aggregated totals are removed:

-   `Malaysia`
-   `All States`

### 4. Monthly Aggregation

Both datasets are aggregated to **state-month level**.

Fish landings:

    sum(landings) by state + year_month

Weather variables:

    mean(weather variables) by state + year_month

### 5. Dataset Merge

The datasets are merged on:

-   `state`
-   `year_month`

Join type:

    Left Join

### 6. Missing Value Handling

A multi-step approach is used:

1.  **Linear interpolation within each state**
2.  **State-level mean imputation**
3.  **Global fallback mean (for remaining values)**

------------------------------------------------------------------------

## Final Processed Dataset

The cleaned dataset (`processed_data.csv`) contains:

  Property      Value
  ------------- ------------
  States        14
  Months        72
  Total rows    1008
  Time period   2018--2023

Each row represents:

    State + Month

------------------------------------------------------------------------

## Exploratory Data Analysis

The training notebook includes:

-   Descriptive statistics
-   Time-series visualization of national fish landings
-   Correlation heatmaps between landings and climate variables
-   State-level variation analysis

Key insights:

-   Clear seasonal patterns exist
-   Strong temporal autocorrelation in fish landings
-   Some correlation with climate variables

------------------------------------------------------------------------

## Baseline Models

Three baseline models are evaluated.

### Seasonal Naive

Uses landings from the same month in the previous year.

### ETS (Exponential Smoothing)

Configuration:

-   Additive trend
-   Additive seasonality
-   Seasonal period = 12 months

### Linear Regression

Uses averaged national climate features to predict national landings.

### Baseline Performance

ETS produced the strongest baseline performance.

------------------------------------------------------------------------

## Feature Engineering

The following features are created for the machine learning models.

### Time Features

-   `year`
-   One-hot encoded `month`

### Lag Features

-   `landings_lag1`
-   `landings_lag12`

### Weather Features

Selected predictors:

-   temperature
-   humidity
-   pressure
-   wind_speed
-   dew_point

### Monsoon Feature

Binary indicator:

    1 if month in [Nov, Dec, Jan, Feb]
    0 otherwise

------------------------------------------------------------------------

## Main Model: XGBoost

The primary model used is **XGBoost Regressor**.

### Train-Test Split

Training:

    2018 – 2022

Testing:

    2023

This preserves time ordering to avoid leakage.

------------------------------------------------------------------------

## Model Configurations Tested

Four XGBoost configurations are tested.

### Config A --- Temporal Only

Features:

-   year
-   month dummies

------------------------------------------------------------------------

### Config B --- Add Lag Features

Adds:

-   landings_lag1
-   landings_lag12

------------------------------------------------------------------------

### Config C --- Add Weather Variables

Adds:

-   temperature
-   humidity
-   pressure
-   wind_speed
-   dew_point

------------------------------------------------------------------------

### Config D --- Full Model

Adds:

-   monsoon_flag

This configuration produced the best performance.

------------------------------------------------------------------------

## LSTM Model

A deep learning benchmark using **Long Short-Term Memory (LSTM)** is
also implemented.

### Architecture

    LSTM (32 units)
    Dropout (0.2)
    Dense (16, ReLU)
    Dense (1)

### Input Features

-   landings
-   temperature
-   humidity
-   pressure
-   wind_speed
-   dew_point

Sequence length:

    12 months

Early stopping is used during training.

------------------------------------------------------------------------

## Key Findings

### 1. XGBoost Outperforms LSTM

The tree-based model achieved significantly lower prediction error.

### 2. Lag Features Are Critical

Recent historical landings strongly influence predictions.

### 3. Weather Variables Improve Accuracy

Adding climate variables improved forecasting performance.

### 4. Monsoon Seasonality Adds Signal

Seasonal regime information slightly improves predictions.

### 5. Tree Models Work Better for Small Panel Data

Because each state only has 72 monthly observations, gradient boosting
models outperform deep learning.

------------------------------------------------------------------------

## Forecast Generation

After evaluation, the best XGBoost model is retrained using **all
available data (2018--2023)**.

Future forecasts are generated for:

    January 2024 – December 2030

### Forecast Strategy

1.  Generate future monthly rows for all states
2.  Use recursive prediction
3.  Update lag features dynamically

------------------------------------------------------------------------

## Climate Scenario Assumptions

Two climate scenarios are used for future inputs.

### Scenario 1: Naive Climate

Uses **2023 monthly averages**.

### Scenario 2: Historical Monthly Average

Uses **average climate values from 2018--2023**.

------------------------------------------------------------------------

## Prediction Intervals

Forecast uncertainty is estimated using **bootstrap residual sampling**.

Intervals generated:

-   **80% interval**
-   **95% interval**

------------------------------------------------------------------------

## Outputs

### Processed Dataset

    processed_data.csv

### Forecast Dataset

    forecasts_2030.csv

Forecast dataset columns:

-   state
-   year_month
-   predicted_landings
-   lower_80
-   upper_80
-   lower_95
-   upper_95

------------------------------------------------------------------------

## Methods Summary

### Statistical

-   Seasonal naive forecasting
-   ETS exponential smoothing

### Machine Learning

-   Linear regression
-   XGBoost

### Deep Learning

-   LSTM neural networks

### Data Engineering

-   Monthly aggregation
-   Feature engineering
-   Lag feature creation
-   Missing value imputation

------------------------------------------------------------------------

## Limitations

-   Future climate values are approximated rather than projected
-   Only one test year is used for evaluation
-   No rolling cross-validation implemented
-   LSTM likely limited by small dataset size

------------------------------------------------------------------------

## Future Improvements

Possible improvements include:

-   Rolling time-series cross validation
-   Testing LightGBM and CatBoost
-   Using real climate projections
-   Incorporating fisheries policy or economic indicators
-   Adding spatial modeling between states

------------------------------------------------------------------------

## Conclusion

This project demonstrates a complete pipeline for **state-level fish
landing forecasting in Malaysia**.

The key result is that:

**XGBoost with lag, climate, and monsoon features provides the most
accurate forecasts**, outperforming statistical baselines and deep
learning approaches.

The final output is a **monthly forecast dataset through 2030 with
prediction intervals**, enabling planning and fisheries resource
management.
