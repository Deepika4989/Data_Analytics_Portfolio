# Data_Analytics_Portfolio Projects
## 1. Customer Churn Prediction
Customer Churn Prediction

Predicts whether a customer will churn based on tenure, monthly charges, support call volume, and contract type. Built end-to-end in Google Colab using pandas and scikit-learn.

Dataset

1,000 customer records, 5 columns: tenure_months, monthly_charges, support_calls, contract_type, churned. Class distribution is imbalanced — 69.1% churned (691), 30.9% retained (309).

Key EDA finding

Churn rate varies sharply by contract type:

Contract Type	Churn Rate
Month-to-month	79.9%
One year	62.3%
Two year	46.3%

Shorter commitment contracts churn far more — the single strongest signal in the data.

Approach
One-hot encoded contract_type (drop-first)
80/20 train-test split (random_state=42)
Trained two models: Logistic Regression and Random Forest
Results
Model	Accuracy
Logistic Regression	74.0%
Random Forest	74.5%

Both models land at nearly identical accuracy, but disagree on what drives churn:

Logistic Regression ranks contract type as most influential (two-year contracts strongly reduce churn odds; coefficient −2.04)
Random Forest ranks monthly_charges (0.40) and tenure_months (0.39) as the top features, with contract type well behind

This split is worth noting explicitly — it's a real interpretability discussion, not a discrepancy to paper over. Linear models and tree-based models can reasonably disagree on feature importance because they capture different kinds of relationships (linear/additive vs. non-linear/interaction effects).

Both models show the same weakness: recall on the minority "not churned" class (0) is notably lower (48% and 45%) than on churned customers (87% and 89%) — a direct effect of the class imbalance.

Tools

Python, pandas, matplotlib, scikit-learn (LogisticRegression, RandomForestClassifier, train_test_split, accuracy_score, confusion_matrix, classification_report)

Possible next steps
Address class imbalance (e.g. class weighting, SMOTE)
Try gradient boosting for comparison
Feature engineering — interaction terms between tenure and contract type

## 2. Customer Segmentation with K-Means Clustering
Customer Segmentation with K-Means Clustering

## Overview
This project applies **unsupervised learning (K-Means clustering)** to segment customers based on their income and spending behavior, using the [Customer Personality Analysis dataset](https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis) from Kaggle.

Unlike prior projects in this portfolio (churn prediction, Spotify streams, global budgets), this one has **no target label** — the goal is to let the algorithm discover natural customer groups on its own, purely from patterns in the data.

## Objective
Segment ~2,200 customers into meaningful groups based on **Income** and **Total Spend**, to demonstrate how a business could use unsupervised learning for targeted marketing, pricing strategy, or retention efforts — without needing pre-labeled customer categories.

## Dataset
- **Source:** Kaggle — Customer Personality Analysis
- **Rows:** 2,240 customers, 29 original features
- **Key features used:** `Income`, and an engineered `TotalSpend` (sum of spending across wine, fruit, meat, fish, sweets, and gold product categories)

## Methodology

### 1. Data Cleaning
- Dropped 24 rows with missing `Income`
- Identified and removed an extreme outlier (`Income` = 666,666) via histogram inspection — this single value was severely distorting the distribution and would have disproportionately influenced distance-based clustering
- Filtered remaining data to `Income < 150,000` for a clean, usable distribution

### 2. Feature Engineering
- Created `TotalSpend` by summing six individual spending category columns (`MntWines`, `MntFruits`, `MntMeatProducts`, `MntFishProducts`, `MntSweetProducts`, `MntGoldProds`)

### 3. Feature Scaling
- Applied `StandardScaler` to `Income` and `TotalSpend`, since K-Means is a distance-based algorithm — without scaling, `Income`'s much larger numeric range would have dominated the clustering over `TotalSpend`

### 4. Choosing K — Elbow Method
- Ran K-Means across K=1 to K=10, plotting inertia (sum of squared distances to centroid) at each value
- Identified the elbow bend at **K=5**, where the inertia curve transitions from a steep drop to a flattened, diminishing-returns slope

### 5. Model Fitting
- Fit `KMeans(n_clusters=5, random_state=42, n_init=10)` on the scaled features
- `n_init=10` runs multiple K-Means++ initializations and keeps the best result, avoiding poor outcomes from unlucky random centroid placement

### 6. Validation
- **Silhouette Score: 0.41** — indicates reasonably well-separated, meaningful clusters (score range: -1 to 1, with values further above 0 indicating better-defined clusters)
- Cluster sizes ranged from 315 to 575 customers — no single cluster dominated or was negligibly small, supporting K=5 as a sound choice

### 7. Interpretation
Cluster centers were examined by averaging `Income` and `TotalSpend` within each group, then labeled based on business meaning:

| Cluster | Avg. Income | Avg. Total Spend | Segment |
|---|---|---|---|
| 0 | ~$22,700 | ~$70 | Budget Customers |
| 4 | ~$40,300 | ~$139 | Low-Value Customers |
| 2 | ~$55,900 | ~$488 | Moderate Customers |
| 1 | ~$69,500 | ~$1,042 | Active Spenders |
| 3 | ~$79,500 | ~$1,703 | Premium Customers |

## Results
The five segments form a clear, monotonic progression across both income and spending — customers cluster naturally along an income/spend axis rather than in irregular or overlapping groups. This suggests income is a strong (though not sole) driver of spending behavior in this dataset, and that a simple two-feature clustering already produces business-actionable segments.

![Customer Segments](customer_segments_kmeans.png)

## Key Takeaways
- **Outlier handling matters more in unsupervised learning** than it might first appear — a single distorted data point can meaningfully shift centroid positions and, in turn, an entire cluster's boundaries, since there's no label to "correct" the model.
- **The Elbow Method and Silhouette Score work best together** — inertia alone always decreases as K increases, so silhouette score provides an independent check that the chosen K produces genuinely separated (not just numerically lower-inertia) clusters.
- **Cluster interpretation is a manual, domain-driven step** — the algorithm only produces numeric groupings; turning "Cluster 3" into "Premium Customers" requires examining the underlying feature averages and applying business judgment.

## Possible Extensions
- Incorporate additional features (e.g., `Recency`, `NumWebPurchases`, `Kidhome`) for richer, higher-dimensional segmentation
- Compare K-Means results against DBSCAN or Hierarchical Clustering to check robustness of the groupings
- Use cluster labels as an engineered feature in a downstream supervised model (e.g., predicting campaign response)

## Tools
`Python`, `pandas`, `scikit-learn` (`StandardScaler`, `KMeans`, `silhouette_score`), `matplotlib`
