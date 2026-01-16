# Technical Project Analysis: Corporación Favorita Retail Sales Forecasting

## Introduction
In retail operations, the ability to predict future demand is critical for inventory management and financial planning. This Corporación Favorita Retail Sales Forecasting project, conducted as part of the Masterschool Data Science Time Series Project, focused on building a reliable forecasting system for a one-year window from August 16, 2016, to August 15, 2017.

Beyond historical analysis, the primary goal of this study was to identify a high-performance predictive framework that can be advised for future use within the company’s operations. By isolating the most effective modeling techniques and data-handling strategies, this project provides a scalable solution that will be recommended for future demand planning and automated replenishment cycles.

## 1. Data Strategy & The "Filtered Train" Logic
The raw transaction data was approximately 5GB (125M rows), posing a significant computational challenge. To ensure stability and focus on high-signal data, a deliberate Filtered Train strategy was executed:

* **Geographic & Category Focus:** To isolate consistent consumer behavior, we filtered the data for the 'Guayas' region (using stores.csv) and the Top 3 product families: Grocery I, Beverages, and Cleaning (using items.csv).
* **Chunk-Based Processing:** To handle the file size, we processed the data in 1,000,000-row chunks, ensuring that the environment remained stable during the merge and filter operations.
* **Strategic Sampling:** We extracted a 300,000-row slice for our modeling. This size was carefully chosen to be large enough to preserve complex seasonal patterns while remaining efficient for iterative model testing.
* **Supplemental Dataset Integration:** * **Holidays & Events:** We merged holidays_events.csv to map specific sales spikes to national and local festivities.
    * **Transactions:** Used to cross-reference sales volume with overall store foot traffic.
* **Feature Selection (Oil Prices):** Following a correlation analysis of the oil.csv dataset, it was determined that oil prices did not significantly influence daily sales for the selected product categories. Consequently, oil was excluded to allow the models to focus on the more impactful internal calendar events.

## 2. Strategic Rationale for Model Selection
Based on the exploratory analysis conducted in the [Data Prep](https://github.com/reharabi/favorita-retail-sales-forecasting/blob/main/Notebooks/Data_Prep%20(2).ipynb) notebook, we identified specific data characteristics that dictated our choice of three distinct modeling approaches:

1. **Addressing Non-Linear Volatility (XGBoost):** Our data preparation revealed that retail sales are defined by "spikes" during events and holidays. We chose XGBoost because gradient boosting is uniquely equipped to handle these non-linear shifts and complex interactions between multiple categorical flags that traditional linear models often ignore.
2. **Decomposing Overlapping Seasonality (Facebook Prophet):** The EDA confirmed a very strong "Weekly Heartbeat" (weekend peaks) co-existing with long-term yearly trends. We selected Facebook Prophet because its additive model excels at breaking down these multi-layered seasonalities into interpretable components.
3. **Statistical Benchmark & Autocorrelation (ARIMA):** To validate the performance of machine learning, we required a statistical baseline. ARIMA was chosen to test how much of the sales could be predicted purely through historical autocorrelation (yesterday’s sales predicting today’s) versus the more complex, feature-driven approaches.

## 3. Model Logic and Implementation

### A. Extreme Gradient Boosting (XGBoost) [view notebook](https://github.com/reharabi/favorita-retail-sales-forecasting/blob/main/Notebooks/XGboost%20(3).ipynb)

**Operational Rationale:**
XGBoost was selected as our primary high-performance algorithm due to its superior ability to handle non-linear volatility. Unlike traditional models, XGBoost identifies complex intersections between calendar events—such as paydays coinciding with holiday weekends—that significantly drive retail volume in the Guayas region.

**Technical Architecture & Implementation:**
* **Feature Engineering (The Signal Builder):** Raw date information was transformed into a multi-dimensional feature set, including dayofweek, quarter, month, and dayofyear. Binary markers were engineered for Month-Start/End to capture specific consumer purchasing cycles.
* **Hyperparameter Optimization:** To ensure the model could capture high-resolution patterns without "memorizing" random noise, we utilized:
    * **Up to 5,000 Estimators:** This provided the model with sufficient capacity to fine-tune its response to rare but impactful holiday events.
    * **Max Depth of 3:** By restricting tree depth, we forced the model to prioritize broad, recurring seasonal trends over individual day anomalies.
    * **Early Stopping Logic:** We implemented early_stopping_rounds=100. This served as a critical efficiency guardrail, automatically terminating the training process if the test error did not improve for 100 consecutive iterations. This ensured the model stopped at the point of peak generalization, preventing it from over-fitting to historical training data.

**The Feature Elimination Study (Strategic Refinement):**
We conducted a three-stage competitive test to isolate the most effective drivers of sales accuracy:
1. **The Baseline (Calendar Only):** Established a strong initial performance using only standard date features. (MAE: 269.27 | RMSE: 484.99).
2. **The High-Complexity Model:** We introduced Holidays & Events (which natively included the 2016 Earthquake impact) and a binary Weekend Flag. Result: Performance dropped (MAE: 269.63). Our analysis concluded that the is_weekend flag was redundant, as the model was already extracting this information from the dayofweek feature. This redundancy introduced statistical noise that degraded the model’s precision.
3. **The Targeted Model (Winner):** By executing feature elimination to remove the noisy weekend markers while retaining the high-impact Holidays & Events data, we achieved our project-best performance. (MAE: 267.65 | RMSE: 483.87).

**Stakeholder Value:**
The "Targeted" XGBoost model is our champion configuration. It proves that for Favorita’s operations, a streamlined feature set—focused on core calendar drivers and verified holiday events—provides the most reliable and accurate forecast for the one-year planning horizon.

### B. Facebook Prophet (Additive Decomposition) [view notebook](https://github.com/reharabi/favorita-retail-sales-forecasting/blob/main/Notebooks/MYProphet%20(4).ipynb)

**Operational Rationale:**
Facebook Prophet was integrated into our framework due to its specialized capability in decomposing time series data into interpretable components: trend, weekly cycles, and yearly seasonality. For Corporación Favorita, this model serves as a vital tool for understanding the "Why" behind sales fluctuations, providing a transparent view of the underlying 7-day shopping cycle.

**Technical Architecture & Implementation:**
* **Component Extraction:** We utilized Prophet’s additive seasonality to isolate the Weekly Heartbeat. The analysis identified a clean peak on Saturdays and Sundays, which were confirmed as the primary volume drivers for the Grocery and Beverage categories in the Guayas region.
* **Holiday & Event Integration:** We utilized the holidays_events.csv file to map specific festive impacts. This included the 2016 Earthquake, treated as a historical event to help the model distinguish between standard seasonal dips and unique external shocks.

**The Experimental Comparison (Internal vs. External Drivers):**
We executed a competitive study between two configurations to determine the optimal level of complexity for the 1-year forecast:
1. **The Baseline Model (Calendar Focus):** Relying strictly on the internal daily and weekly seasonal components automatically detected by the algorithm. (MAE: 285.75 | RMSE: 485.06)
2. **The Enhanced Model (High Detail):** We introduced external regressor flags for holidays and the earthquake recovery period. Result: MAE: 288.40 | RMSE: 484.23

**Observation:**
While the RMSE showed a negligible improvement (0.17%), the MAE increased by 0.93%. This suggests that adding external regressors caused the model to "over-fit" to past anomalies that did not repeat in a predictable way during the 2017 test window.

**Final Findings & Verdict:**
* **Generalization over Complexity:** The Baseline Model is the strategic winner for operational use. It provided a lower average error (MAE), indicating that for these specific product families, the regular weekly rhythm is a more reliable predictor than the specific holiday weights assigned by the model.
* **Interpretation of Spikes:** The component analysis revealed that when external flags were added, the model created "messy spikes" in its holiday adjustment graph. This confirms that many recorded events had a negligible impact on actual sales, and including them only added unnecessary variance to the forecast.

**Stakeholder Value:**
Prophet remains our most powerful explanatory tool. By identifying that a Baseline approach is superior, we have simplified the operational pipeline for Favorita—proving that the store's 7-day shopping cycle is the most critical and stable driver for inventory planning.

### C. ARIMA/SARIMAX (Statistical Autoregression) [view notebook](

**Operational Rationale:**
The ARIMA (AutoRegressive Integrated Moving Average) approach was implemented as our classical statistical benchmark. This model is essential for understanding how much of the future sales can be predicted purely through historical "autocorrelation"—the relationship between a day’s sales and the sales from previous days and weeks.

**Technical Architecture & Implementation:**
* **Stationarity & Stability (ADF Test):** We performed an Augmented Dickey-Fuller (ADF) test to determine if the data mean was stable over time. Result: A p-value of 0.0589 was returned. Since this was slightly above the 0.05 threshold, the data was classified as non-stationary, requiring first-order differencing (d=1) to stabilize the dataset for modeling.
* **Parameter Selection Strategy:** We used auto_arima for initial algorithmic selection and analyzed ACF/PACF plots to identify the underlying 7-day cyclical patterns (seasonality s=7).
* **Exogenous Factor Integration:** The model was upgraded to a SARIMAX structure by incorporating the holidays_events.csv as an external regressor. This allowed the statistical model to adjust its expectations during peak periods like New Year’s and historical anomalies like the 2016 earthquake.

**The "Persistence" Breakthrough (Manual Refinement):**
Our technical analysis revealed a common failure in automated statistical forecasting: the model's tendency to revert to a "flat mean" over long-term horizons.
* **The Problem:** The initial algorithmic model correctly identified short-term trends but failed to maintain the 7-day heartbeat across the full 1-year test period.
* **The Solution:** We manually overrode the parameters by introducing Seasonal Autoregression (p=1) and Seasonal Differencing (d=1). This forced the model to preserve the persistent weekly cycles necessary for retail inventory planning.

**The Competitive Comparison:**
The manual refinement proved that statistical models require specific manual adjustments to reach operational standards:
* **Baseline Model (Automated):** MAE: 342.59 | RMSE: 547.65
* **Enhanced Model (Manual Refinement):** MAE: 301.25 | RMSE: 520.22.
* **Performance Gain:** This intervention resulted in a 12% accuracy improvement and a forecast that accurately reflects the recurring weekly retail cycle rather than a flat average.

**Stakeholder Value:**
While SARIMAX was more conservative than XGBoost, it provided the most reliable baseline for identifying the "minimum expected volume." By correctly tuning the seasonal components, we have created a statistical safety net that ensures inventory never falls below the identified weekly threshold.

## 4. Quick Model Comparison

| Model Strategy | MAE (Lower is Better) | RMSE (Outlier Sensitivity) | Verdict |
| :--- | :--- | :--- | :--- |
| XGBoost (Targeted) | 267.65 | 483.87 | Champion: Most accurate and responsive to retail spikes. |
| Prophet (Baseline) | 285.75 | 485.06 | Explanatory: Best for visualizing trend components. |
| SARIMAX (Enhanced) | 301.25 | 520.22 | Baseline: Reliable after manual seasonal tuning. |

## 5. Advice to Favorita: Next Steps
1. **Standardize XGBoost Operations:** Implement the "Targeted" XGBoost configuration for daily planning, as it provides the highest precision during critical holiday surges.
2. **Prioritize Signal over Noise:** Avoid over-engineering features. Our results across all models show that redundant features (like manual weekend flags) degrade accuracy. Stick to high-signal calendar drivers.
3. **Staffing Optimization:** Use the "Weekly Heartbeat" identified in our models to align store staffing and logistics with the Saturday–Sunday volume peaks.
4. **Continuous Calibration:** Re-train models every six months to adapt to shifting consumer patterns in the Guayas region and ensure the "Targeted" feature sets remain optimized.
