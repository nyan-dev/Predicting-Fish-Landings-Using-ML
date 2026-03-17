graph TD

    subgraph A["Section A: Data Prep & National Aggregation"]
        A1[Aggregate National Landings]
        A2[Aggregate National Climate Covariates]
        A3[Merge Data (National_df)]
        A4[Train/Test Split (National_df)]
        A1 --> A2 --> A3 --> A4
    end

    subgraph B["Section B: Baseline Model Estimation & Evaluation"]
        B1[ETS Model Estimation & Forecast]
        B2[ETS Point Metrics]
        B3[SARIMA Auto-Selection & Estimation]
        B4[SARIMA Forecast & PIs]
        B5[SARIMA All Metrics]
        B1 --> B2
        B3 --> B4 --> B5
    end

    subgraph C["Section C: XGBoost Model Estimation (National)"]
        C1[Feature Engineering Config C]
        C2[XGBoost Config C Estimation & Forecast]
        C3[XGBoost Config C Point Metrics]
        C4[Feature Engineering Config D]
        C5[XGBoost Config D Estimation & Forecast]
        C6[XGBoost Config D Point Metrics]
        C1 --> C2 --> C3
        C4 --> C5 --> C6
    end

    subgraph DE["Sections D & E: Uncertainty & Recalibration"]
        DE1[Compute Training Residuals (C & D)]
        DE2[Generate Initial Bootstrap PIs]
        DE3[Evaluate Initial PIs (0% PICP Detected)]
        DE4[Recalibrate PIs (Scaling Factor)]
        DE5[Evaluate Recalibrated PIs]
        DE6[Select Best XGBoost Model (Config C)]
        DE1 --> DE2 --> DE3
        DE3 -->|Problem: 0% PICP| DE4
        DE4 --> DE5 --> DE6
    end

    subgraph F["Section F: Future Forecasting (2024-2030)"]
        F1[Refit Best XGBoost Model on Full Data]
        F2[Generate Future Climate Scenario]
        F3[Recursive Monthly Forecasts (2024-2030)]
        F4[Generate Recalibrated PIs for Forecast]
        F5[Visualize National Forecast]
        F6[Generate Annual Summary Table]
        F7[Visualize State-by-State Forecasts]
        F1 --> F2 --> F3 --> F4 --> F5 --> F6 --> F7
    end

    A4 -->|National Train/Test Data| B1
    A4 -->|National Train/Test Data| B3
    A4 -->|National Train/Test Data| C1
    A4 -->|National Train/Test Data| C4

    C3 -->|Point Metrics C| DE1
    C6 -->|Point Metrics D| DE1

    DE6 -->|Selected Model & Scaled Residuals| F1
