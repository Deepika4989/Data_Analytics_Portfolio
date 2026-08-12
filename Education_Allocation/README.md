# Education Budget Allocation Prediction

## Overview
This project predicts a country's annual **education spending (in billions USD)** using historical government budget data spanning 45 countries and 80 years (1946-2026). It compares a Linear Regression baseline against a Random Forest model, and includes an error-analysis pass to understand *where* and *why* the models struggle most.

## Objective
Predict `Education_Amount_Billions_USD` from a country's `Year`, `Total_Budget_Billions_USD`, and `Country` identity, then evaluate prediction accuracy at both the aggregate and individual-record level.

## Dataset
- **Source:** Master Global Budgets Historical dataset
- **Rows:** 3,654 (45 countries × multiple years, 1946-2026)
- **Columns:** 21, including budget category percentages (Defense, Education, Health, Infrastructure, Interest Payments) and their corresponding dollar amounts
- No missing values across any column

## Methodology

### 1. Data Preparation
- Verified no missing values across the dataset
- Reviewed summary statistics for `Year`, `Education_Percentage`, and `Education_Amount_Billions_USD` to understand the target's scale and spread

### 2. Feature Encoding
- One-hot encoded the `Country` column (`pd.get_dummies`, dropping the first category to avoid multicollinearity — Argentina became the implicit baseline)
- Final feature set: `Year`, `Total_Budget_Billions_USD`, and 44 country indicator columns

### 3. Train/Test Split
- Standard 80/20 split (`test_size=0.2, random_state=42`) — 2,923 training rows, 731 test rows

### 4. Model 1 — Linear Regression (Baseline)
- **MAE: 5.50**
- **R²: 0.9859**
- Strong baseline performance — country identity and total budget size are highly predictive of education spend, largely because education spend is a roughly consistent percentage-share pattern within each country over time

### 5. Model 2 — Random Forest Regressor
- **MAE: 1.91**
- **R²: 0.9960**
- Clear improvement over Linear Regression, cutting MAE by roughly two-thirds — suggesting some non-linear, country-specific patterns in how education allocation shifts relative to total budget size that a linear model couldn't fully capture

### 6. Error Analysis
- Sorted predictions by absolute error to identify which records the models struggled with most
- The **largest absolute errors were concentrated in the USA and China** — unsurprising, since these countries have by far the largest total budgets, so even a small percentage error translates into a large absolute dollar error
- Calculated **Mean Absolute Percentage Error (12.49%)** for the Random Forest model to get a scale-independent view of error
- The largest *percentage* errors told a different story: **Indonesia and South Korea in earlier years** (1969-1990) had the worst relative predictions — likely because these countries' budgets were still small and rapidly changing during those decades, making the pattern harder to learn from limited historical data at that scale

### 7. Additional Exploration
- Checked correlation between `Defense_Percentage` and `Education_Percentage` (0.058) — negligible correlation, suggesting these two budget categories don't meaningfully trade off against each other in a simple linear way across this dataset

## Key Takeaways
- **Absolute error and percentage error tell different stories.** The countries with the "worst" predictions by raw dollar error (USA, China) were actually well-predicted proportionally — they just have large budgets. The real weak spots (Indonesia, South Korea in earlier decades) only became visible by normalizing for scale.
- **Random Forest's improvement suggests non-linear, country-specific effects.** The jump from R²=0.986 to R²=0.996 indicates budget allocation isn't a purely linear function of year and total budget — different countries likely shift their education priorities at different rates or thresholds.
- **High R² doesn't mean uniformly accurate predictions.** Despite excellent overall R², the 12.49% MAPE and country-specific error analysis show real, meaningful prediction gaps still exist for smaller or historically volatile economies.

## Possible Extensions
- Add more contextual features (GDP, population, political regime data) to help explain the harder-to-predict cases like Indonesia and South Korea
- Model each budget category (Defense, Health, Infrastructure) simultaneously to explore whether categories trade off against each other over time
- Build a time-series-aware model (e.g., per-country forecasting) rather than treating each country-year as an independent row, since spending patterns are likely autocorrelated within a country over time

## Tools
`Python`, `pandas`, `scikit-learn` (`LinearRegression`, `RandomForestRegressor`, `train_test_split`, `mean_absolute_error`, `r2_score`)
