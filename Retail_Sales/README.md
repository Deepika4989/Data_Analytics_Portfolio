# Retail Sales EDA

## Overview
An exploratory data analysis of 1,000 retail transactions across three product categories, examining spending patterns by category, gender, and time — with a focus on clean data preparation before analysis.

## Objective
Understand retail transaction behavior: which product categories drive the most revenue and volume, whether spending differs by gender, and how total sales trend over the course of a year.

## Dataset
- **Source:** Retail Sales transaction dataset
- **Rows:** 1,000 transactions
- **Columns:** 9 — Transaction ID, Date, Customer ID, Gender, Age, Product Category, Quantity, Price per Unit, Total Amount
- **Product Categories:** Clothing (351 transactions), Electronics (342), Beauty (307)
- No missing values across any column

## Methodology

### 1. Data Loading & Initial Inspection
- Loaded the dataset and reviewed structure with `.info()` and `.head()`

### 2. Datetime Parsing Fix
- The `Date` column loaded as a plain object/string (`dd-mm-yyyy` format), which would have blocked any time-based analysis or sorting
- Converted to proper datetime using `pd.to_datetime(df['Date'], format='%d-%m-%Y')`, explicitly specifying the format to avoid pandas misparsing day/month order — a common silent error when dates are ambiguous (e.g., is `01-02-2023` Jan 2nd or Feb 1st?)

### 3. Category-Level Analysis
- Compared average transaction value (`Total Amount`) across product categories:
  - **Beauty:** $467.48 average
  - **Electronics:** $458.79 average
  - **Clothing:** $443.25 average
- Compared average quantity purchased per transaction — all three categories sit close together (2.48-2.55 units), showing quantity doesn't vary much by category, while dollar value per transaction does

### 4. Gender-Based Spending Comparison
- Compared average `Total Amount` by gender: **Female $456.55** vs. **Male $455.43** — a negligible difference, suggesting gender is not a meaningful driver of transaction value in this dataset

### 5. Visualizations
- **Bar chart:** average transaction value by product category, to visually compare the three categories side by side
- **Time series line chart:** total monthly sales across the full year, using `.dt.to_period('M')` to group transactions by month and reveal any seasonal trend

## Key Findings
- **Beauty products generate the highest average transaction value**, despite having the fewest total transactions — suggesting higher-priced items or larger basket sizes per purchase in that category compared to Clothing and Electronics
- **Purchase quantity is consistent across categories**, meaning the revenue difference between categories comes from price per unit, not how many items customers buy per transaction
- **Gender shows no meaningful effect on spending** — average transaction values are nearly identical between male and female customers
- **The datetime parsing step was a necessary first fix**, not just a formatting nicety — without it, the monthly sales trend analysis wouldn't have been possible at all

## Key Takeaways
- **Date parsing deserves explicit attention, not defaults.** Letting pandas auto-infer date format risks silent misparsing; specifying the format string directly removes that ambiguity.
- **Quantity and dollar value can tell different stories.** Looking at only one (e.g., units sold) would have missed that Beauty's revenue edge comes from price, not volume.
- **Not every comparison reveals a strong pattern, and that's a valid finding.** The near-identical gender spending averages are worth reporting as-is rather than searching for a difference that isn't really there.

## Possible Extensions
- Break down monthly sales trend by product category to see if seasonality differs across categories (e.g., Clothing spiking in specific months)
- Segment by `Age` bands to check whether spending behavior varies more by age than by gender
- Add day-of-week analysis to see if certain days drive disproportionate sales

## Tools
`Python`, `pandas`, `matplotlib`, Google Colab
