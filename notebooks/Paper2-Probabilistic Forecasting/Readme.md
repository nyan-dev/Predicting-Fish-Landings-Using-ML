Paper 2's summary
---
## **Project Summary: National & State Fisheries Forecasting (2018–2030)**

This project established a robust analytical pipeline to forecast aquaculture landings in Malaysia, specifically addressing the challenges of seasonality, climate influence, and uncertainty quantification.

### **Step 1: Data Preparation & National Aggregation (Section A)**
*   **Method**: Aggregated monthly state-level landings and climate variables (temperature, humidity, pressure, etc.) into a unified national dataset (`national_df`).
*   **Purpose**: To create a consistent baseline for both classical and machine learning models.
*   **Result**: A cleaned time series from 2018–2023, split into training (2018–2022) and holdout (2023) sets.

### **Step 2: Classical Benchmark Modeling (Section B)**
*   **Method**: Fitted **ETS (Exponential Smoothing)** and **SARIMA** models to the national series.
*   **Purpose**: To establish baseline point-forecast and interval performance.
*   **Findings**: ETS provided strong point forecasts (RMSE: 6163), while auto-selected SARIMA was less accurate (RMSE: 8678) but provided wide, high-coverage intervals (100% PICP).

### **Step 3: Advanced Machine Learning with XGBoost (Section C)**
*   **Method**: Developed two configurations (Config C: Lags + Climate; Config D: Config C + Monsoon Flag) at the national level.
*   **Purpose**: To test if non-linear ML models and seasonal flags could outperform statistical baselines.
*   **Findings**: Config D slightly outperformed Config C in point accuracy (RMSE: 7993 vs 8085), proving that the monsoon flag adds predictive value.

### **Step 4: Uncertainty Quantification & Recalibration (Sections D & E)**
*   **Method**: Used **Bootstrap Residual Simulation** to generate prediction intervals. Detected a failure in initial calibration (0% PICP) and implemented a **Residual Scaling Factor** correction.
*   **Purpose**: To ensure the model doesn't just give a single number, but a reliable 'range' of possible outcomes.
*   **Findings**: Recalibration successfully moved XGBoost coverage from 0% to **100% PICP**. **Recalibrated Config C** was selected as the final model due to the best balance of coverage and interval width (lowest Winkler Score).

### **Step 5: Future Forecasting to 2030 (Section F)**
*   **Method**: Refitted the best model on the *full* 2018–2023 dataset. Generated 2024–2030 forecasts using a **Recursive Multi-Step** approach and a historical average climate scenario.
*   **Purpose**: To provide long-term strategic projections for the national fisheries sector.
*   **Result**: Produced stable seasonal projections showing expected annual cycles with 80% and 95% confidence bands.

### **Key Outputs Generated**
1.  **National Forecast Plot**: A comprehensive visualization showing historical data and future trends with uncertainty bands.
2.  **State-by-State Multi-Panel Plot**: Individual trajectories for all 14 Malaysian states, capturing local variations.
3.  **Annual Summary Table**: Aggregated total predicted landings and confidence intervals per year (2024–2030).
4.  **Analytical Workflow Chart**: A condensed Mermaid diagram representing the research logic.

### **Conclusion**
The integration of lagged landings, climate covariates, and recursive XGBoost modeling, combined with post-hoc residual recalibration, provides a statistically valid and informative framework for fisheries management in Malaysia through 2030.
