## 🚀 Project Highlights
* **Big Data Handling:** Engineered a pipeline to process 5GB of transaction data using 1M-row chunking.
* **Model Champion:** XGBoost achieved the lowest error (**MAE: 267.65**) by identifying non-linear payday surges.
* **Actionable Insight:** Isolated the "Weekly Heartbeat," identifying Saturday/Sunday as 60%+ of volume drivers.
* **Strategic Refinement:** Improved SARIMAX accuracy by 12% through manual seasonal parameter tuning.


# Corporación Favorita: Retail Sales Forecasting


## Project Overview
This project presents a robust time-series forecasting framework developed for Corporación Favorita, Ecuador's largest grocery retailer. The primary objective was to transition from traditional inventory estimation to a data-driven system capable of supporting future demand planning and automated replenishment cycles. 

By focusing on the Guayas region and the Top 3 product families (Grocery I, Beverages, and Cleaning), the project evaluates the trade-offs between machine learning, additive decomposition, and classical statistical models. The study covers a one-year testing window from August 16, 2016, to August 15, 2017.

## Tools and Technologies Used
The following stack was utilized to manage data processing, modeling, and evaluation:

* **Programming Language:** Python 3.x
* **Data Manipulation:** Pandas (Chunk-processing for large-scale datasets), NumPy
* **Machine Learning:** XGBoost (Extreme Gradient Boosting)
* **Time Series Analysis:** Facebook Prophet, Statsmodels (SARIMAX)
* **Data Visualization:** Matplotlib, Seaborn
* **Environment:** Jupyter Notebooks 

## Repository Structure
The repository is organized into a modular pipeline to ensure reproducibility:

1.  **01_Data_Preparation.ipynb**: Includes the "Filtered Train" logic, regional segmentation, and signal cleaning (removal of non-correlating oil price data).
2.  **02_XGBoost_Model.ipynb**: Implementation of a High-Estimator (5k) Gradient Boosting model with early stopping to capture non-linear volatility.
3.  **03_Prophet_Model.ipynb**: Analysis of time-series components to isolate the "Weekly Heartbeat" of retail demand.
4.  **04_ARIMA_Model.ipynb**: A statistical approach using manually tuned Seasonal Autoregression to establish a persistent sales baseline.
5.  **Technical_Analysis.md**: A comprehensive document detailing the methodology, feature engineering rationale, and strategic findings.

## Model Performance Metrics
Models were evaluated based on their ability to minimize error during high-volatility retail events (holidays and paydays).

| Model Strategy | Mean Absolute Error (MAE) | Root Mean Squared Error (RMSE) | Strategic Verdict |
| :--- | :--- | :--- | :--- |
| **XGBoost (Targeted)** | **267.65** | **483.87** | **Champion**: Optimal for automated replenishment. |
| **Prophet (Baseline)** | **285.75** | **485.06** | **Explanatory**: Best for seasonal decomposition. |
| **SARIMAX (Manual)** | **301.25** | **520.22** | **Baseline**: Reliable statistical floor. |

## Methodology and Data Strategy
To ensure the output was viable for automated replenishment, several strategic decisions were made:

* **Noise Reduction:** Correlation studies confirmed that global oil prices acted as a distracting variable for daily grocery sales; therefore, they were excluded to improve model generalization.
* **Weekly Heartbeat Identification:** Decomposition revealed that Friday through Sunday accounts for over 60% of weekly volume, indicating that replenishment cycles should be front-loaded toward the mid-week.
* **Stability over Complexity:** Tests showed that leaner feature sets (focusing on core calendar events) outperformed over-engineered models, reducing the risk of over-fitting to historical anomalies like the 2016 earthquake.

## Conclusion and Business Impact
The implementation of the Targeted XGBoost model provides a scalable solution for Corporación Favorita. By moving toward this automated framework, the organization can achieve higher accuracy in stock-level maintenance, reduced waste in perishable categories, and optimized labor scheduling aligned with identified demand peaks.

## Prepared by :
**Author:** Reha Rabi  
Masterschool Data Science Course  
**Date:** January 2026

**Contact:** rehaballendo@gmail.com
