# Rainfall Time Series Forecasting using SARIMA

##  Project Overview

This project focuses on **time series analysis and rainfall forecasting** using historical monthly rainfall data.

The objective is to analyze rainfall patterns, identify seasonality, and forecast:

* **Total monthly rainfall for the next 12 months**
* **Maximum rainfall recorded in a single day for the next month**

A **SARIMA (Seasonal ARIMA)** model is used because rainfall data shows recurring seasonal patterns across months.

The project covers the complete time series workflow:

**Data Loading → Data Merging → EDA → Seasonality Analysis → Stationarity Testing → Auto ARIMA → SARIMA Modeling → Forecasting → Residual Analysis**

---

##  Objectives

The main objectives of this project are:

* Analyze historical rainfall patterns
* Understand monthly rainfall seasonality
* Test the stationarity of the time series
* Automatically identify suitable SARIMA parameters
* Forecast total monthly rainfall for the next 12 months
* Forecast maximum daily rainfall for the next month
* Visualize historical trends and future forecasts
* Analyze model residuals and forecast uncertainty

---

##  Dataset

The project uses three monthly rainfall datasets:

### 1. Total Monthly Rainfall

**File:** `rainfall-monthly-total.csv`

Contains the total rainfall recorded during each month.

| Column           | Description                   |
| ---------------- | ----------------------------- |
| `month`          | Month and year                |
| `total_rainfall` | Total rainfall in millimeters |

### 2. Number of Rainy Days

**File:** `rainfall-monthly-number-of-rain-days.csv`

Contains the number of rainy days recorded in each month.

| Column             | Description          |
| ------------------ | -------------------- |
| `month`            | Month and year       |
| `no_of_rainy_days` | Number of rainy days |

### 3. Highest Daily Rainfall

**File:** `rainfall-monthly-highest-daily-total.csv`

Contains the maximum rainfall recorded during a single day in each month.

| Column                      | Description                  |
| --------------------------- | ---------------------------- |
| `month`                     | Month and year               |
| `maximum_rainfall_in_a_day` | Maximum daily rainfall in mm |

The three datasets are merged using the `month` column to create a unified monthly time series dataset.

---

##  Dataset Summary

The final dataset contains **462 monthly observations**, covering:

**January 1982 – June 2020**

After merging, the main variables are:

* `total_rainfall`
* `no_of_rainy_days`
* `maximum_rainfall_in_a_day`

The `month` column is converted to a datetime index for time series analysis.

---

##  Technologies & Libraries

* Python
* Pandas
* NumPy
* Matplotlib
* Seaborn
* Statsmodels
* pmdarima

### Key Libraries Used

| Library     | Purpose                              |
| ----------- | ------------------------------------ |
| Pandas      | Data loading, cleaning and merging   |
| NumPy       | Numerical operations                 |
| Matplotlib  | Time series visualization            |
| Seaborn     | Statistical visualization            |
| Statsmodels | ADF test and SARIMA modeling         |
| pmdarima    | Automatic SARIMA parameter selection |

---

##  Data Preprocessing

### 1. Load Multiple Datasets

The three rainfall CSV files are loaded using Pandas.

### 2. Datetime Conversion

The `month` column is converted into a datetime format using:

```python
pd.to_datetime()
```

### 3. Dataset Merging

The datasets are merged on the common `month` column.

### 4. Time Series Index

The `month` column is set as the DataFrame index.

### 5. Seasonality Feature

A `month_num` feature is created from the datetime index to analyze monthly rainfall seasonality.

---

##  Exploratory Data Analysis

Several visualizations were created to understand the rainfall data.

### Total Rainfall Over Time

A time series line plot was used to visualize changes in total monthly rainfall from 1982 to 2020.

### Monthly Seasonality

A boxplot was created to compare rainfall distributions across different months.

The analysis shows recurring seasonal patterns, with higher rainfall during certain months.

This seasonal behavior supports the use of a **seasonal time series model**.

---

##  Stationarity Test

The **Augmented Dickey-Fuller (ADF) test** was performed to check whether the total rainfall time series was stationary.

### Results

| Metric        |           Value |
| ------------- | --------------: |
| ADF Statistic |     **-5.3168** |
| p-value       | **5.05 × 10⁻⁶** |

Since the p-value is significantly below **0.05**, the null hypothesis of a unit root is rejected.

### Conclusion

The `total_rainfall` series was considered **stationary based on the ADF test**, so no differencing was required for the Auto ARIMA model selected for the main SARIMA analysis.

---

##  Auto ARIMA

`pmdarima.auto_arima()` was used to automatically search for suitable ARIMA/SARIMA parameters.

A seasonal period of:

```text
m = 12
```

was used because the dataset contains monthly observations and rainfall can exhibit yearly seasonality.

### Selected Model

```text
SARIMAX(1, 0, 0) × (2, 0, 0, 12)
```

This corresponds to:

* Non-seasonal order: `(1, 0, 0)`
* Seasonal order: `(2, 0, 0, 12)`
* Seasonal period: `12 months`

The selected model had an AIC of approximately:

**5634.483**

---

##  SARIMA Model

The selected SARIMA configuration was fitted using `statsmodels`.

```text
Order: (1, 0, 0)
Seasonal Order: (2, 0, 0, 12)
```

### Important Model Coefficients

| Parameter       | Coefficient |
| --------------- | ----------: |
| AR(1)           |      0.2024 |
| Seasonal AR(12) |      0.4441 |
| Seasonal AR(24) |      0.3723 |

The seasonal terms capture relationships between observations separated by yearly intervals.

---

##  Residual Analysis

Residual analysis was performed using:

* Residual histogram
* Q-Q plot
* Ljung-Box test

The Ljung-Box result was:

```text
p-value ≈ 0.09
```

This does not provide strong evidence of remaining autocorrelation at the tested lag.

The residual analysis was used to assess whether the fitted model adequately captured the time-dependent structure.

---

#  Forecasting

## 1. Total Monthly Rainfall Forecast

The SARIMA model was used to forecast the next **12 months** after June 2020.

### Forecast Results

| Month    | Forecast (mm) |
| -------- | ------------: |
| Jul 2020 |         76.66 |
| Aug 2020 |         54.14 |
| Sep 2020 |         64.62 |
| Oct 2020 |        165.93 |
| Nov 2020 |        124.19 |
| Dec 2020 |    **251.46** |
| Jan 2021 |         62.94 |
| Feb 2021 |     **40.63** |
| Mar 2021 |         75.20 |
| Apr 2021 |        148.57 |
| May 2021 |        139.21 |
| Jun 2021 |        168.54 |

### Key Observation

According to the model forecast:

* **Highest predicted monthly rainfall:** December 2020 — **251.46 mm**
* **Lowest predicted monthly rainfall:** February 2021 — **40.63 mm**

The forecast also includes 95% confidence intervals to represent forecast uncertainty.

---

## 2. Maximum Daily Rainfall Forecast

A separate SARIMA model was applied to:

```text
maximum_rainfall_in_a_day
```

The model generated a forecast for the next month.

### Forecast

| Metric                            |         Value |
| --------------------------------- | ------------: |
| Forecasted maximum daily rainfall |  **29.42 mm** |
| Lower 95% CI                      | **-36.77 mm** |
| Upper 95% CI                      |  **95.62 mm** |

### Important Note

The negative lower confidence bound is **not physically meaningful**, because rainfall cannot be negative.

It indicates that the statistical forecast has substantial uncertainty. In practical applications, forecast intervals should be constrained or transformed appropriately to respect the non-negative nature of rainfall.

---

##  Forecast Visualization

The project generates visualizations showing:

* Historical rainfall
* Forecasted rainfall
* Forecast start point
* 95% confidence intervals

These plots make it easier to understand the expected rainfall pattern and the uncertainty around future predictions.

---

##  Challenges

### 1. Seasonal Rainfall Patterns

Rainfall shows strong month-to-month variation and seasonal behavior.

**Approach:**
Used SARIMA with a seasonal period of 12 months.

### 2. Model Selection

Selecting appropriate ARIMA and seasonal parameters can be difficult.

**Approach:**
Used `Auto ARIMA` to search for suitable model parameters.

### 3. Forecast Uncertainty

Rainfall is highly variable, resulting in relatively wide confidence intervals.

**Approach:**
Confidence intervals were included with the forecasts to communicate uncertainty.

### 4. Non-negative Forecast Requirement

Statistical confidence intervals can sometimes produce negative lower bounds.

**Approach:**
The limitation was identified and documented; future versions can use non-negative transformations or constrained forecasting methods.

---

##  Future Improvements

Potential improvements include:

* Implementing proper train/test or rolling-origin time series validation
* Comparing SARIMA with Prophet, ETS, and machine learning models
* Adding lag features
* Adding rolling mean and rolling standard deviation features
* Evaluating forecasts using MAE, RMSE and MAPE
* Using transformations such as log/Box-Cox for highly skewed rainfall data
* Applying models specifically designed for non-negative rainfall distributions
* Building an interactive forecasting dashboard using Streamlit

---

##  Key Learning Outcomes

This project provided practical experience in:

* Time series data preprocessing
* Datetime handling with Pandas
* Exploratory time series analysis
* Seasonality identification
* Stationarity testing using ADF
* Auto ARIMA
* SARIMA modeling
* Residual diagnostics
* Multi-step forecasting
* Confidence interval interpretation
* Time series visualization

---

##  Project Structure

```text
Rainfall-Time-Series/
│
├── Rainfall_Time_Series.ipynb
└── README.md
```

The original datasets are not included in the repository.

---

##  How to Run

### 1. Clone the repository

```bash
git clone https://github.com/VSK-AI/Rainfall-Time-Series.git
```

### 2. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn statsmodels pmdarima
```

### 3. Open the notebook

Open the Rainfall Time Series notebook using:

* Jupyter Notebook
* JupyterLab
* Google Colab

### 4. Update dataset paths

Update the CSV file paths in the notebook according to the location of your dataset.

### 5. Run the notebook

Run the cells sequentially to reproduce:

* Data preprocessing
* EDA
* Stationarity testing
* Auto ARIMA
* SARIMA modeling
* Residual analysis
* 12-month rainfall forecasting
* Maximum daily rainfall forecasting

**GitHub:** [VSK-AI](https://github.com/VSK-AI)
