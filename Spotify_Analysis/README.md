# Spotify Artist & Streaming Analysis

## Overview
This project explores a Spotify streaming dataset to understand what drives (or fails to drive) an artist's streaming performance, using both exploratory data analysis and supervised machine learning. A key outcome of this project is a **documented null result** — an honest finding that stream counts could not be meaningfully predicted from the available features, and an explanation of why that itself is a valid and informative conclusion.

## Objective
Predict artist streaming counts from song/artist-level features, while practicing rigorous data leakage detection, target transformation for skewed distributions, and systematic model comparison.

## Dataset
- **Source:** Spotify streaming dataset (Kaggle)
- Contains song/artist-level attributes alongside stream counts and derived audio/popularity metrics

## Methodology

### 1. Exploratory Data Analysis
- Investigated the distribution of the target variable (`Streams`), which was **heavily right-skewed** — a small number of artists with massive stream counts, and the vast majority with far lower totals
- Reviewed feature correlations and column definitions closely, which surfaced a critical issue in the next step

### 2. Data Leakage Detection
- Identified that several columns were **algebraically derived from the target itself** (or from other columns tightly coupled to it) — meaning the model could "cheat" by learning a mathematical shortcut rather than genuine predictive patterns
- These leaking columns were removed before modeling, since leaving them in would have produced artificially inflated performance that wouldn't generalize to new data

### 3. Target Transformation
- Applied a **log1p transformation** (`log(1 + x)`) to the `Streams` target to address the severe right skew
- This compresses the influence of extreme high-stream outliers and makes the target distribution far more suitable for regression modeling, which assumes roughly normal, homoscedastic residuals

### 4. Handling High-Cardinality Categoricals
- Several categorical features (e.g., artist name, genre tags) had very high cardinality — too many unique values to one-hot encode directly without exploding dimensionality
- Bucketed these into a smaller number of meaningful groups before encoding, to keep the feature space manageable and reduce overfitting risk

### 5. Model Sweep
- Trained and compared **nine different regression models** (spanning linear models, tree-based models, and ensemble methods) on the cleaned, transformed data
- Evaluated using standard regression metrics (MAE, RMSE, R²)

## Results
Across all nine models, **R² scores were effectively null (at or near zero)** — none of the models were able to meaningfully explain variance in streaming counts using the available features, even after addressing leakage and target skew.

## Key Takeaways
- **A null result is still a real result.** Rather than treating this as a failed project, the null R² was documented as a legitimate finding: it suggests that streaming success, at least with the features available in this dataset, is likely driven by external factors not captured here (marketing, playlist placement, virality, release timing, artist fanbase size at time of release, etc.) rather than the song/artist attributes provided.
- **Data leakage checks come before model selection, not after.** Catching the algebraically derived columns early prevented a misleadingly "successful" model that would have failed in any real deployment.
- **Target transformation and model choice are separate levers.** Log-transforming a skewed target is a data preparation step that benefits any downstream model — it doesn't replace the need to also pick an appropriate model family.
- **Negative/null results are worth reporting honestly**, especially in a portfolio context — they demonstrate rigor, methodological soundness, and the ability to recognize when a dataset genuinely lacks predictive signal, rather than forcing a misleading positive result.

## Tools
`Python`, `pandas`, `numpy`, `scikit-learn`, `matplotlib`/`seaborn`

## Possible Extensions
- Incorporate external signals if available (playlist inclusion counts, social media mentions, release date proximity to trends)
- Reframe the problem as classification (e.g., "hit" vs. "not hit," using a streams threshold) rather than exact stream-count regression
- Explore artist-level aggregation instead of song-level, to reduce noise from individual track variability
