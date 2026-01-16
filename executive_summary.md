# Executive Summary: Corporación Favorita Retail Sales Forecasting

## Overview
This project successfully developed and evaluated a high-performance demand forecasting system for Corporación Favorita, specifically targeting the Guayas region and its Top 3 product families (Grocery I, Beverages, and Cleaning). The objective was to transition from historical analysis to a proactive, automated replenishment strategy for the period of August 2016 through August 2017.

## Strategic Data Approach
To manage a massive 5GB dataset, we implemented a "Filtered Train" logic. By isolating high-signal data and removing non-impactful variables—such as oil prices—we created a streamlined dataset that prioritized the "Weekly Heartbeat" of the retail cycle. This strategy ensured environmental stability and maximized predictive accuracy.

## Modeling Performance & Key Findings
We tested three distinct frameworks to determine the most reliable predictor for daily operations:

### The Champion: XGBoost (Targeted Configuration)
* **Result:** MAE: 267.65 | RMSE: 483.87
* **Why it won:** XGBoost’s ability to handle non-linear spikes during paydays and holidays made it the most precise tool for daily inventory planning. Through feature elimination, we found that a streamlined model—focusing on core calendar drivers without redundant "weekend flags"—delivered the highest stability.

### The Explainer: Facebook Prophet (Baseline)
* **Result:** MAE: 285.75 | RMSE: 485.06
* **Insight:** Prophet proved that the store's 7-day shopping cycle is its most stable driver. Interestingly, the "Baseline" model outperformed high-complexity versions, proving that over-fitting to past anomalies can actually degrade future forecasts.

### The Safety Net: SARIMAX (Enhanced)
* **Result:** MAE: 301.25 | RMSE: 520.22
* **Improvement:** Through manual parameter tuning (Seasonal Autoregression), we achieved a 12% accuracy gain over automated models. SARIMAX serves as a reliable statistical floor for identifying minimum expected volumes.



## Operational Recommendations
Based on the technical results, we advise the following roadmap for Favorita:

1. **Deployment:** Standardize the Targeted XGBoost model for daily replenishment cycles. Its responsiveness to high-volatility spikes makes it the superior choice for reducing stockouts.
2. **Resource Allocation:** Align logistics and staffing with the identified Saturday-Sunday volume peaks, which our models confirmed as the primary drivers of sales.
3. **Efficiency:** Maintain streamlined feature sets. Both XGBoost and Prophet results show that "less is more"—focusing on high-impact calendar events yields better results than adding complex, noisy variables.
4. **Maintenance:** Re-calibrate the models every six months to ensure they adapt to evolving consumer behaviors in the Guayas region.

## Conclusion
By implementing the Targeted XGBoost framework, Corporación Favorita can achieve a more precise, data-driven approach to inventory management. This system provides the balance of daily accuracy and long-term trend visibility necessary for scalable, automated retail operations.
