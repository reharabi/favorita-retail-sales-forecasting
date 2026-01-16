# Quick Start Guide: Retail Forecasting Pipeline

This guide outlines the steps to replicate the sales forecasting framework for the Corporación Favorita (Guayas Region) project.

## 1. Environment Setup

This project is built using Python 3.12. It is recommended to use a virtual environment to prevent library conflicts.

### Install Dependencies
Run the following command in your terminal or command prompt to install all necessary libraries:

```bash
pip install pandas numpy matplotlib seaborn xgboost prophet statsmodels
```

## 2. Dataset Requirements
The notebooks are designed to process the official Corporación Favorita datasets. Ensure the following files are in your project directory:
* `train.csv` (Main transaction data)
* `stores.csv` (Regional metadata)
* `holidays_events.csv` (Event and holiday metadata)
* `items.csv` (Product family metadata)

## 3. Execution Order
To maintain the data pipeline integrity and ensure replenishment logic is applied correctly, execute the notebooks in this specific sequence:

### Step 1: Data Preparation
**File:** `01_Data_Prep.ipynb`  
**Purpose:** Processes raw data using 1M-row chunking, filters for the Guayas region, and exports the high-signal 300,000-row modeling slice.

### Step 2: XGBoost Forecasting (Champion Model)
**File:** `02_XGBoost_Model.ipynb`  
**Purpose:** Implements the 5,000-estimator gradient boosting model. This notebook contains the feature elimination study that achieved the project-best MAE of 267.65.

### Step 3: Seasonal Decomposition
**File:** `03_Prophet_Model.ipynb`  
**Purpose:** Analyzes the "Weekly Heartbeat." Execute this to see the additive breakdown of Friday-Sunday sales surges.

### Step 4: Statistical Baseline
**File:** `04_SARIMAX_Model.ipynb`  
**Purpose:** Runs the statistical benchmark with manual $P=1$ tuning. This notebook demonstrates the 12% improvement over automated ARIMA.

## 4. Key Configuration Parameters
If you wish to modify the models, the following parameters are the core "levers" for accuracy:
* **XGBoost:** `n_estimators=5000`, `max_depth=3`, `early_stopping_rounds=100`.
* **SARIMAX:** `seasonal_order=(1, 1, 2, 7)` for the 7-day retail cycle.
* **Test Period:** Fixed from **August 16, 2016, to August 15, 2017**.

## 5. Visualizing Results
Each notebook will generate a final comparison plot displaying:
1. **Historical Training Data**
2. **Actual Test Data** (Ground Truth)
3. **Model Forecast** (Predicted Values)
