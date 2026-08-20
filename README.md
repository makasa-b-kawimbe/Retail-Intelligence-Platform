# Retail Intelligence Platform: Demand Forecasting, Customer Segmentation, and Personalized Recommendations

**Author:** Makasa Kawimbe

**Notebook:** [Retail Intelligence Platform.ipynb](Programming%20Files/Retail%20Intelligence%20Platform_FINAL.ipynb)

---

## Problem Statement

Retailers face two costly and often disconnected challenges: ordering too much or too little stock, and recommending products that customers have no interest in. When these decisions are made independently — demand planning in one team, marketing in another — the result is overstocked warehouses, missed sales, and generic promotions that treat every customer the same.

This project asks: **Can we use a retailer's own transaction history to simultaneously predict what customers will buy, how much of it will be needed, and which specific products to show each individual?**

Using two years of real purchase data from H&M (approximately 31.8 million transactions across 1.37 million customers and 105,000 products), this project builds a unified retail intelligence system with three capabilities:

1. **Customer Segmentation** — grouping customers by their actual shopping behavior so that marketing and service decisions can be tailored to each group
2. **Demand Forecasting** — predicting how many items will be sold in each product category each week, enabling better inventory planning
3. **Personalized Recommendations** — suggesting the right products to the right customers based on what similar shoppers have bought

---

## Model Outcomes

This project applies both **supervised learning** (where the model learns from historical examples with known outcomes) and **unsupervised learning** (where the model discovers patterns in data without predefined labels).

| Workstream | Type of Learning | What the Model Produces |
|---|---|---|
| Customer Segmentation | Unsupervised | A label for each customer assigning them to one of four behavioral groups |
| Demand Forecasting | Supervised (Regression) | A predicted weekly sales volume for each product category |
| Recommendations | Unsupervised / Collaborative | A ranked list of up to 10 product suggestions per customer |

**Final Results at a Glance:**

| Goal | Benchmark (no model) | With this system | Improvement |
|---|---|---|---|
| Forecast accuracy (% error) | 24.7% average error | 10.9% average error | 56% more accurate |
| Recommendation relevance | 0.74% hit rate | 1.24% hit rate | 68% more relevant |
| Product variety in recommendations | <0.1% of catalogue | 15.9% of catalogue | Far broader discovery |

In plain terms: the forecasting model cuts inventory planning error roughly in half. The recommendation system is 68% more likely to suggest a product the customer actually buys, and it draws from a much wider range of products rather than recycling the same bestsellers.

---

## Data Acquisition

All data comes from the **H&M Personalized Fashion Recommendations** dataset, publicly available on Kaggle. It covers September 2018 through September 2020 and consists of three linked files:

| File | What it contains | Size |
|---|---|---|
| `articles.csv` | Product details: type, category, color, department | 105,542 products |
| `customers.csv` | Customer profiles: age, membership status, communication preferences | 1,371,980 customers |
| `transactions_train.csv` | Full purchase history: who bought what, when, and at what price | 31,788,324 transactions |

The richness of this dataset — combining individual customer behavior, product attributes, and a long purchase history — makes it suitable for all three analytical goals. The two-year time window is long enough to capture seasonal patterns (important for forecasting) while remaining recent enough to be representative of current customer behavior.

---

## Data Preprocessing

Before any modeling could begin, the raw data required significant preparation.

**Handling missing information:**
- Approximately 15% of customers had no recorded age. Rather than discarding these customers, missing ages were filled in using the median age of similar customers (based on membership status and purchase behavior).
- Several customer profile fields (membership status, newsletter subscription, club membership) had small numbers of missing entries, which were filled with the most common value.

**Removing bad data:**
- Duplicate transaction records were identified and removed.
- Price outliers — transactions at unusually high prices likely representing data entry errors — were capped at the 99th percentile rather than deleted, as even outlier customers are useful for the recommendation system.

**Splitting data into training and test sets:**
Rather than splitting data randomly, the data was divided by time — a deliberate choice to mirror how the system would work in production (you can only use past data to predict the future):

| Period | Dates | Purpose |
|---|---|---|
| Training | Sep 2019 – Jun 2020 | Used to build all models |
| Validation | Jun – Aug 2020 | Used to tune and select the best model |
| Test (holdout) | Aug – Sep 2020 | Final performance check on unseen data |

**Feature engineering** — creating new meaningful variables from raw data — was central to this project. From raw transaction records, features such as "days since last purchase," "number of distinct product types bought," "proportion of purchases made online," and "average spend per visit" were computed for each customer to support segmentation and recommendations.

---

## Modeling

Three separate modeling approaches were developed, one for each business goal.

**1. Customer Segmentation — K-Means Clustering**
K-Means clustering groups customers by similarity across behavioral features. After testing groupings from 2 to 10 clusters, four groups were selected as the most interpretable and actionable for the business:

| Segment | Size | Behavior |
|---|---|---|
| Lapsed / Occasional | 46% of customers | Recently acquired but inactive; longest gap since last purchase |
| Active Regular | 33% | Consistent shoppers with diverse product interests; the core revenue base |
| Occasional In-Store | 15% | Infrequent buyers who prefer physical stores |
| Recent / Loyal (VIP) | 6% | Highest-spend, most frequent, most online-oriented customers |

**2. Demand Forecasting — Random Forest**
Three models were built and compared against a simple benchmark (a "seasonal naive" forecast that assumes this week's sales will match the same week last year):

- Linear Regression — a straightforward statistical approach
- **Random Forest** — an ensemble of decision trees that captures complex non-linear patterns; selected as the best performer
- XGBoost — a high-performance gradient boosting algorithm

Random Forest reduced forecast error by 56% compared to the naive benchmark across 13 product categories.

**3. Recommendations — Item-Item Collaborative Filtering**
Collaborative filtering identifies products that tend to be bought together across the customer base. When a customer has bought item A, the system recommends items that other customers who bought A also purchased. A hybrid fallback combines this with segment-level popularity to handle new customers with limited purchase history.

---

## Model Evaluation

Each workstream used evaluation metrics appropriate to its task, selected to reflect real business impact rather than abstract statistical accuracy.

**Segmentation:** The Silhouette Score (0.22) measures how clearly separated the four customer groups are — a positive score confirms the segments are meaningfully distinct rather than arbitrary.

**Forecasting:** Weighted Absolute Percentage Error (WAPE) measures the average percentage by which forecasts miss actual sales. Lower is better. The Random Forest achieved 10.9% WAPE, compared to 24.7% for the no-model baseline — meaning for every £100,000 of planned inventory, the model reduces average planning error from £24,700 to £10,900.

**Recommendations:** Recall@10 measures whether a product the customer actually bought appears in the top 10 recommendations. The collaborative filtering model achieved 1.24% Recall@10, a 68% improvement over simply recommending the most popular products. Coverage — the share of the product catalogue that gets recommended — increased from under 0.1% (popularity) to 15.9% (collaborative filtering), meaning far more products get discovered by customers.

**One important nuance:** The highest-value customers (the VIP segment, 6% of the base) are actually the hardest to make accurate recommendations for, because their broad and diverse purchase histories make their preferences harder to predict. This is the primary area for future improvement — a more sophisticated model specifically designed for power users would unlock the most commercial value.

---

## Key Findings and Recommendations

1. **A small group of customers drives disproportionate value.** The 6% of customers in the VIP segment are the most frequent, highest-spending, and most online-native shoppers. Protecting their retention and investing in better recommendations for them should be a commercial priority.

2. **Nearly half of all customers are at risk of lapsing.** The largest single segment (46%) has the widest gap since their last purchase. Targeted reactivation campaigns — personalized to their past purchase history — offer a significant growth opportunity.

3. **Inventory planning can be materially improved.** Cutting forecast error in half at the category level translates directly to fewer markdowns and fewer stock-outs. The model is production-ready for category-level open-to-buy planning.

4. **Bestseller-only recommendations are leaving revenue on the table.** The current approach of recommending popular products exposes customers to less than 0.1% of the catalogue. The collaborative filtering model draws from 15.9%, surfacing relevant products that would otherwise go undiscovered.

5. **Next steps:** Validate all models on the final holdout test period; build a real-time signal layer (live inventory and pricing); develop a specialized recommendation model for high-value customers; and establish an A/B testing framework to measure actual revenue lift.

---

*UC Berkeley Professional Certificate in Machine Learning & Artificial Intelligence*
*Makasa Kawimbe*
