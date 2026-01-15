# Technical Project Analysis: Corporación Favorita Retail Sales Forecasting

## Introduction
In retail operations, the ability to predict future demand is critical for inventory management and financial planning. This **Corporación Favorita Retail Sales Forecasting** project, conducted as part of the **Masterschool Data Science Time Series Project**, focused on building a reliable forecasting system for a one-year window from **August 16, 2016, to August 15, 2017**.

Beyond historical analysis, the primary goal of this study was to identify a high-performance predictive framework for **future demand planning and automated replenishment cycles**. By isolating the most effective modeling techniques, this project provides a scalable solution for operational use.

---

## 1. Data Strategy & The "Filtered Train" Logic
The raw transaction data posed a significant computational challenge. To ensure stability and focus on high-signal data, a deliberate **Filtered Train** strategy was executed:

* **Geographic & Category Focus:** To isolate consistent consumer behavior, we filtered for the **'Guayas' region** and the Top 3 product families: **Grocery I, Beverages, and Cleaning**.
* **Chunk-Based Processing:** To handle the file size, we processed the data in **1,000,000-row chunks**, ensuring environment stability during merge and filter operations.
* **Strategic Sampling:** We extracted a **300,000-row slice** for modeling. This size was carefully chosen to preserve complex seasonal patterns while remaining efficient for iterative testing.
* **Supplemental Dataset Integration:** * **Holidays & Events:** Mapped specific sales spikes to national and local festivities.
    * **Transactions:** Used to cross-reference sales volume with store foot traffic.
* **Feature Selection (Oil Prices):** Following a correlation analysis, it was determined that oil prices did not significantly influence daily sales for these categories. Consequently, oil data was excluded to prevent model noise.

---

## 2. Strategic Rationale for Model Selection
Three distinct modeling approaches were chosen based on specific data characteristics:

1.  **XGBoost (Non-Linear Volatility):** Chosen because gradient boosting is uniquely equipped to handle the "spikes" caused by paydays and complex holiday interactions.
2.  **Facebook Prophet (Overlapping Seasonality):** Selected for its ability to decompose multi-layered seasonalities, such as the "Weekly Heartbeat" (weekend peaks) versus long-term trends.
3.  **ARIMA/SARIMAX (Statistical Benchmark):** Implemented to test how much of the sales could be predicted purely through historical autocorrelation versus feature-driven machine learning.

---

## 3. Model Logic and Implementation

### A. Extreme Gradient Boosting (XGBoost)
**Operational Rationale:** XGBoost was our primary high-performance algorithm. It identifies complex intersections between calendar events—such as paydays coinciding with holiday weekends—that drive retail volume in the Guayas region.

**Technical Architecture:**
* **Feature Engineering:** Raw dates were transformed into `dayofweek`, `quarter`, `month`, and `dayofyear`. Binary markers were engineered for Month-Start/End.
* **Hyperparameters:** 5,000 Estimators and a Max Depth of 3.
* **Early Stopping:** Implemented `early_stopping_rounds=100` to terminate training at the point of peak generalization, preventing over-fitting.

**Strategic Refinement (Feature Elimination Study):**
* **The Baseline (Calendar Only):** MAE: 269.27 | RMSE: 484.99.
* **The High-Complexity Model:** Included Holidays and a binary Weekend Flag. **Result:** Performance dropped (MAE: 271.83) as the weekend flag introduced redundant noise.
* **The Targeted Model (Winner):** Removed noisy markers while retaining high-impact Holiday data. **Result: MAE: 267.65 | RMSE: 483.87.**

### B. Facebook Prophet (Additive Decomposition)
**Operational Rationale:** Used to understand the "Why" behind fluctuations. Prophet serves as a vital tool for transparently viewing the underlying 7-day shopping cycle.

**Findings & Verdict:**
* **The "Weekly Heartbeat":** Analysis confirmed a clean peak on Saturdays and Sundays.
* **Generalization over Complexity:** The **Baseline Model (MAE: 285.75)** outperformed the Enhanced Model (MAE: 288.40). This proved that the regular weekly rhythm is a more reliable predictor than specific holiday weights, which caused "messy spikes" in the forecast.

### C. ARIMA/SARIMAX (Statistical Autoregression)
**Operational Rationale:** Essential for understanding "autocorrelation"—the relationship between today’s sales and previous history.

**The "Persistence" Breakthrough:**
* **The Problem:** Initial automated models correctly identified short-term trends but failed to maintain the 7-day heartbeat over a full year, reverting to a "flat mean."
* **The Solution:** We manually overrode parameters, introducing **Seasonal Autoregression (p=1)** and **Seasonal Differencing (d=1)**.
* **Performance Gain:** This intervention resulted in a **12% accuracy improvement** (MAE: 301.25), ensuring a forecast that reflects recurring weekly cycles rather than a flat average.

---

## 4. Quick Model Comparison

| Model Strategy | MAE (Lower is Better) | RMSE (Outlier Sensitivity) | Verdict |
| :--- | :--- | :--- | :--- |
| **XGBoost (Targeted)** | **267.65** | **483.87** | **Champion**: Most accurate and responsive to spikes. |
| **Prophet (Baseline)** | **285.75** | **485.06** | **Explanatory**: Best for trend visualization. |
| **SARIMAX (Enhanced)** | **301.25** | **520.22** | **Baseline**: Reliable after manual seasonal tuning. |

---

## 5. Advice to Favorita: Next Steps
1.  **Standardize XGBoost Operations:** Implement the "Targeted" XGBoost configuration for daily planning to handle critical holiday surges.
2.  **Prioritize Signal over Noise:** Avoid over-engineering features. Redundant features (like manual weekend flags) degrade accuracy.
3.  **Staffing Optimization:** Use the identified "Weekly Heartbeat" to align store staffing and logistics with Saturday–Sunday volume peaks.
4.  **Continuous Calibration:** Re-train models every six months to adapt to shifting consumer patterns in the Guayas region.
