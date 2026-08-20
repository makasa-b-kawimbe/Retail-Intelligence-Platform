# Retail Intelligence Platform: Demand Forecasting, Customer Segmentation, and Personalized Recommendations

**Author:** Makasa Kawimbe

**Notebook:** [Retail Intelligence Platform.ipynb](Programming%20Files/Retail%20Intelligence%20Platform_FINAL.ipynb)

## Executive Summary

This project builds a Python-based retail intelligence MVP using historical transaction data from H&M (~31.8M transactions, 1.37M customers, 105K products) to answer three linked business questions: who are the retailer's meaningful customer groups, what is the expected category-level demand, and which products should be recommended to each customer? Initial EDA reveals a young-skewing customer base with heavily right-skewed purchase frequency, strong seasonal transaction patterns, and a product catalog dominated by upper-body Ladieswear. A baseline Logistic Regression model successfully classifies high-value customers (top 25% spenders) with strong ROC-AUC performance, with purchase frequency and product diversity as the strongest predictors.

## Rationale

Retailers must balance two decisions typically modeled separately: anticipating aggregate demand and personalizing product discovery for individual customers. Simple popularity-based recommendations over-recommend narrow product sets, while aggregate forecasts ignore customer-level heterogeneity. A unified analytical approach combining customer segmentation, demand forecasting, and personalized recommendations delivers actionable decision support for inventory planning, targeted marketing, and customer retention.

## Research Question

Can historical customer transactions, product attributes, and customer metadata be leveraged to build a retail intelligence system that (1) segments customers into actionable behavioral groups, (2) forecasts category-level demand with better accuracy than seasonal-naive baselines, and (3) generates personalized product recommendations that outperform global popularity?

## Data Sources

- **H&M Personalized Fashion Recommendations** (Kaggle)
  - `articles.csv` — 105,542 products with hierarchical metadata (type, group, color, department)
  - `customers.csv` — 1,371,980 customers with demographics and engagement indicators
  - `transactions_train.csv` — 31,788,324 purchase records (Sep 2018 – Sep 2020)

## Methodology

1. **Data Cleaning:** Missing value imputation (customers: FN, Active, club status, age), duplicate removal, outlier detection and capping using IQR method
2. **Feature Engineering:** RFM metrics (Recency, Frequency, Monetary), behavioral features (purchase cadence, basket diversity, tenure, channel preference)
3. **Exploratory Analysis:** Distribution analysis, temporal patterns, cross-dataset relationships, correlation analysis
4. **Baseline Modeling:** Logistic Regression for high-value customer classification with ROC-AUC evaluation

## Results

| Metric | Value |
|--------|-------|
| Model | Logistic Regression (balanced class weights) |
| Task | High-Value Customer Classification (top 25% spenders) |
| ROC-AUC | Strong discriminative performance |
| Top Predictors | Purchase frequency, unique articles, tenure |

**Key EDA Findings:**
- Customer age distribution peaks around 20-30 years; median age ~31
- Transaction volume shows clear seasonality with peaks in Sep and Nov-Dec
- Purchase frequency is heavily right-skewed (most customers buy infrequently)
- Online channel (Channel 2) dominates transaction volume
- Strong positive correlation between frequency, unique articles, and total spend
- Price outliers exist but are flagged rather than removed to preserve recommendation signal

## Next Steps

- Apply K-Means clustering on RFM features for customer segmentation
- Build category-level demand forecasting models (seasonal-naive → gradient boosting)
- Implement collaborative filtering and content-based recommendation baselines
- Compare XGBoost/Random Forest against logistic regression baseline

## Contact and Further Information

Makasa Kawimbe  
UC Berkeley Professional Certificate in Machine Learning & Artificial Intelligence
