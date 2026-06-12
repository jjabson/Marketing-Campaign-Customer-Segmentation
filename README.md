# Marketing Campaign Customer Segmentation (KMeans + DBSCAN)

## Overview
This project segments customers into meaningful groups to help marketing teams improve targeting, personalization, and ROI. Using customer demographics, purchase behavior, web engagement, discount sensitivity, and campaign response history, I built a segmentation pipeline and produced actionable customer personas.

## Business Problem
Customer behavior is not uniform—different customers vary by:
- spending level and purchasing frequency,
- channel preference (web/catalog/store),
- discount sensitivity,
- responsiveness to marketing campaigns,
- lifecycle stage (tenure with the company).

The goal is to create customer segments that enable tailored marketing strategies and more efficient allocation of budget and effort.

## Dataset
Each row represents a customer with features such as:
- **Value & spending:** `Income`, product spend amounts (e.g., wines, meat, etc.)
- **Engagement:** `Recency`, number of purchases
- **Channels:** web, catalog, store purchases, and web visits
- **Campaign outcomes:** accepted campaigns (1–5) and final response
- **Demographics:** birth year, education, marital status, children in household

## Key Data Preparation
- **Missing values:** `Income` had 24 missing values (~1.1%). Imputed using the **median**.
- **Outliers / data quality:**
  - Removed unrealistic ages (e.g., >90 years) based on `Year_Birth`.
  - Capped extreme income values to reduce distortion: created **`Income_capped`**.
- **Categorical cleanup:**
  - Consolidated rare `Marital_Status` categories (`YOLO`, `Absurd`, `Alone`) into **`Other`** to improve interpretability.
- **Date parsing fix:**
  - Correctly parsed `Dt_Customer` (day-first format) and recomputed `TenureDays` with **0 missing values**.

## Feature Engineering (Segmentation Signals)
I engineered features that align with marketing strategy and customer behavior:
- **Value:** `TotalSpend`, `Income_capped`
- **Engagement:** `TotalPurchases`, `Recency`
- **Channel behavior:** `NumWebPurchases`, `NumCatalogPurchases`, `NumStorePurchases`, `NumWebVisitsMonth`
- **Discount sensitivity:** `DealRate` (deals purchases relative to total purchases)
- **Marketing responsiveness:** `TotalAccepted` (count of accepted campaigns)
- **Lifecycle:** `TenureDays`
- **Household context:** `ChildrenCount`

All clustering features were standardized using **StandardScaler** (mean≈0, std≈1).

## Dimensionality Reduction (Interpretability + Visualization)
- PCA showed the main structure is driven by:
  - **PC1:** customer value + shopping style (spend, income, deal sensitivity, channel purchases)
  - **PC2:** lifecycle + digital engagement (tenure, web visits/purchases)
- t-SNE was used as a visualization tool to confirm non-random structure (not used for clustering).

## Modeling Approach

### 1) KMeans (Primary Segmentation)
- Silhouette scores:
  - k=2: **0.295** (strongest natural split)
  - k=4: **0.147**
  - k=5: **0.150**
- I selected **k=4** for actionable marketing personas despite expected overlap (common in real customer behavior).

**Cluster sizes (k=4):**
- Cluster 0: 464 (~21%)
- Cluster 1: 498 (~22%)
- Cluster 2: 626 (~28%)
- Cluster 3: 649 (~29%)

### 2) DBSCAN (Validation + Outlier Detection)
- eps sweep (3.0–4.0) consistently produced **1 dense core cluster** + very small noise.
- Final choice: **eps=3.5, min_samples=10**
  - Core cluster: 2225 customers (99.5%)
  - Noise/outliers: 12 customers (0.5%)

DBSCAN indicates the population is largely continuous (one dense core), and is most useful here for **identifying niche outlier behaviors**, not multi-persona segmentation.

## Final Customer Personas (KMeans k=4)

### Cluster 0 — Deal-Seeking Families (High Browsing, Low Spend)
**Profile**
- Lower value (income/spend below average)
- Higher children count
- High deal sensitivity
- Above-average web visits, modest purchasing
**Recommended actions**
- Family bundles, coupons, entry-level offers
- Cart reminders, simplify checkout
- Value messaging (“save more” campaigns)

### Cluster 1 — High-Value Loyalists (Premium Multi-Channel Buyers)
**Profile**
- Highest income and total spend
- Multi-channel buyers (store + catalog + web)
- Lowest deal sensitivity
- Highest campaign responsiveness
**Recommended actions**
- VIP/loyalty perks, early access, premium recommendations
- Personalized offers (not discount-heavy)
- Retention programs to protect revenue

### Cluster 2 — Core Active Shoppers (Store + Digital Power Users)
**Profile**
- High store + high web purchases
- Solid spend and longer tenure
- Moderate deal sensitivity, lower campaign response
**Recommended actions**
- Cross-sell/upsell, product recommendations
- Omnichannel personalization (web + store)
- Targeted messaging to improve campaign lift

### Cluster 3 — Low-Spend Digital Browsers (Low Conversion)
**Profile**
- Lowest income/spend, youngest group
- Highest browsing (web visits), low purchasing
- Higher deal sensitivity, low campaign response
**Recommended actions**
- Onboarding + first-purchase incentives
- Reduce friction (checkout optimization)
- Retargeting and conversion-focused messaging

## DBSCAN Outlier Insight (0.5% of customers)
Noise customers show a distinct niche behavior:
- higher spend than overall,
- much higher web + catalog purchases,
- near-zero store purchases,
- higher deal sensitivity,
- low campaign acceptance.

This suggests a “digital/catalog-heavy, store-avoiding, deal-driven” niche group—useful for specialized channel strategies and offer testing.

## Recommendations
**Deploy KMeans (k=4)** as the primary segmentation model:
- Produces interpretable personas aligned with marketing actions.
- Easy to refresh on a monthly/quarterly schedule.

Use **DBSCAN** as a monitoring tool:
- Flag niche/outlier behaviors for separate review and targeted experiments.

## How to Run
1. Open the notebook: `Marketing_Campaign_Customer_Segmentation.ipynb`
2. Run cells top-to-bottom.
3. Key outputs include:
   - correlation heatmap + EDA plots
   - PCA loadings and t-SNE visualization
   - KMeans silhouette scores, cluster profiles, PCA scatter
   - DBSCAN elbow plot, eps sweep, core vs noise profiling

## Future Improvements
- Segment stability checks over time (drift monitoring)
- A/B tests per segment to measure campaign uplift
- Explore alternative clustering (Gaussian Mixture Models, HDBSCAN) if needed
- Add more features if available (product categories, geography, customer service interactions)

---
**Author:** Jerome Jabson 
**Keywords:** Customer Segmentation, KMeans, DBSCAN, PCA, t-SNE, Marketing Analytics

