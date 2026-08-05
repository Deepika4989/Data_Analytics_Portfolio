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