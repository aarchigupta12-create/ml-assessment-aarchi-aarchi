### B1. PROBLEM FORMULATION

### B1(a) Problem Formulation

The objective is to predict the number of items sold for each store under different promotional strategies.

- **Target Variable:** items_sold (number of items sold per store per month)
- **Input Features:**
  - Store attributes: store size, location type (urban, semi-urban, rural), competition density
  - Promotion type: Flat Discount, BOGO, Free Gift, Category Offer, Loyalty Points
  - Temporal features: month, seasonality, weekends, festivals
  - Customer behavior indicators: footfall, historical sales

- **Type of ML Problem:** Supervised Regression

This is a regression problem because the target variable (items_sold) is continuous. The goal is to predict a numeric outcome rather than classify categories, making regression the appropriate approach.

### B1(b) Target Variable Justification

Using items sold (sales volume) is more reliable than total revenue for this problem.

- Revenue can be influenced by pricing strategies, discounts, and product mix, which may distort the true impact of promotions.
- Items sold directly reflects customer response to promotions and demand generation.
- It provides a clearer measure of promotion effectiveness across stores.

This illustrates a broader principle in machine learning:

> The target variable should align closely with the business objective.

In this case, the goal is to maximize customer engagement and product movement, not just revenue, making items sold a more appropriate and unbiased metric.

### B1(c) Alternative Modelling Strategy

Instead of using a single global model, a more effective approach would be:

- **Segmented or Hierarchical Models**

For example:
- Train separate models for urban, semi-urban, and rural stores
- Or include store-level features and interactions in a unified model

Justification:
- Different store types respond differently to promotions due to variations in customer demographics, purchasing power, and competition.
- A single global model may average out these effects and reduce predictive accuracy.
- Segmented models capture local patterns more effectively, leading to better recommendations.

Additionally, including store_id as a feature or using mixed-effects models allows the model to capture store-specific behavior while still leveraging overall data patterns.

This approach balances generalization with local customization, improving prediction accuracy.


### B2 DATA AND EDA STRATEGY

### B2(a) Data Preparation and Joining Strategy

The data from four tables would be combined using appropriate keys:

- Transactions table: join on store_id and date
- Store attributes: join on store_id
- Promotion details: join on promotion_id or date
- Calendar table: join on date

**Final Dataset Grain:**
- One row per store per month per promotion

**Aggregations:**
- Total items sold per store per month
- Average basket size
- Total footfall
- Promotion applied during the month
- Number of weekends and festival days
- - These analyses also help identify outliers and data quality issues that may impact model performance.

This ensures that the dataset is structured at the same level at which decisions are made (monthly store-level).


### B2(b) Exploratory Data Analysis

The following analyses would be performed:

1. **Promotion vs Items Sold (Bar Chart)**
   - Compare average sales across different promotions
   - Helps identify which promotions are generally effective

2. **Time Series Analysis (Line Plot)**
   - Plot monthly sales trends
   - Identify seasonality and trends (e.g., festive spikes)

3. **Store Segmentation Analysis**
   - Compare performance across urban, semi-urban, and rural stores
   - Helps decide whether segmentation is needed

4. **Correlation Heatmap**
   - Analyze relationships between features such as footfall, competition, and sales
   - Helps in feature selection and identifying multicollinearity

These insights guide feature engineering and model selection by highlighting key drivers of sales.

### B2(c) Promotion Imbalance

If 80% of transactions occur without promotions:

- The model may learn to ignore promotion effects due to underrepresentation.
- It may underestimate the incremental impact of promotions on sales.

To address this:

- Ensure sufficient representation of promotional data through resampling or weighting
- Introduce a binary feature indicating promotion presence
- Consider modelling uplift (incremental impact of promotions) rather than absolute sales

This ensures the model captures the true causal impact of promotions.

## MODEL EVALUATION AND DEPLOYMENT


### B3(a) Train-Test Strategy and Evaluation Metrics

A time-based split should be used:

- Train on the first ~2.5 years
- Test on the most recent 6 months

A random split is inappropriate because:
- It mixes past and future data
- Leads to data leakage
- Produces overly optimistic results

**Evaluation Metrics:**
- RMSE: Measures magnitude of prediction error
- MAE: Measures average absolute error
- R²: Measures how well the model explains variance

Interpretation:
- Lower RMSE and MAE indicate better prediction accuracy
- R² closer to 1 indicates strong explanatory power

Additionally, model performance should be evaluated at the store level to ensure recommendations are reliable across different store types, not just on average.

### B3(b) Model Interpretation Using Feature Importance

Feature importance helps explain why different promotions are recommended for the same store.

For example:
- In December, features like festivals, holidays, and increased footfall may drive the recommendation of Loyalty Points Bonus.
- In March, lower demand or different customer behavior may make Flat Discount more effective.

To communicate this:
- Analyze feature importance scores
- Use tools like SHAP values for deeper insights
- Present findings in simple terms to stakeholders
- Differences in recommendations across months are driven by seasonality and contextual features such as festivals, customer demand patterns, and past performance.

This helps build trust in the model and supports data-driven decision-making.
Differences in recommendations across months are driven by seasonality and contextual features such as festivals, customer demand patterns, and past performance.


### B3(c) Model Deployment and Monitoring

**Deployment Steps:**
1. Train the final model and save it using joblib or pickle
2. At the start of each month:
   - Collect new data (store features, promotions, calendar info)
   - Apply the same preprocessing pipeline
   - Generate predictions for each store

**Automation:**
- Schedule predictions using a pipeline or script

**Monitoring:**
- Track prediction accuracy over time
- Monitor RMSE/MAE on new data
- Detect data drift (changes in input distributions)

If performance degrades:
- Retrain the model using updated data

This ensures the model remains accurate and relevant over time. Additionally, monitor prediction drift to ensure the relationship between features and target remains stable over time.

