# Product Demand Analysis

A machine learning project to estimate product demand using Amazon product metadata.  
This project includes data preprocessing, exploratory analysis, feature engineering, model training, hyperparameter tuning, and prediction export.

## Project Objective

Build a reproducible pipeline that predicts product demand (or demand proxy) from product-level features such as:

- Price and discount information
- Reviews and purchase behavior
- Product/category metadata

## Project Structure

Project/
├── Product_Demand_Analysis.ipynb
├── optimized_demand_model_sgb_tweeedie.pkl
├── Amazon_Products.csv
├── full_dataset_demand_predictions_xgb_optimized.csv
├── requirements.txt
└── README.md


## File Details

### 1. **Product_Demand_Analysis.ipynb** (Main Notebook)
**Purpose:** The complete end-to-end machine learning pipeline.  
**Contains:**
- **Data Import & Cleaning:** Load Amazon data, handle missing values, remove irrelevant columns
- **Exploratory Data Analysis (EDA):** Distribution analysis, correlation matrices, outlier detection, visualization of demand patterns
- **Feature Engineering:** Interaction features, discount factors, categorical encoding, dimensionality reduction
- **Data Preprocessing Pipeline:** Scaling, normalized feature sets ready for modeling
- **Model Development:** Train 5+ regression models (Linear Regression, RandomForest, XGBoost, LightGBM, CatBoost)
- **Model Evaluation:** RMSE, MAE, R² metrics on validation set
- **Hyperparameter Tuning:** GridSearch/RandomSearch for boosting models
- **Final Predictions:** Generate predictions on full dataset and export results
- **Documentation:** 185 cells with detailed markdown sections explaining rationale and interpretation

**How to Use:**
1. Ensure dependencies from `requirements.txt` are installed
2. Open in Jupyter: `jupyter notebook Product_Demand_Analysis.ipynb`
3. Run cells sequentially from top to bottom
4. Check console output and visualizations for model performance

---

### 2. **Amazon_Products.csv** (Input Dataset)
**Size:** 36 MB | **Rows:** 42,676 | **Format:** CSV  
**Purpose:** Raw data source for the analysis.  
**Columns Include:**
- **Product Info:** `product_title`, `product_category`
- **Pricing:** `discounted_price`, `original_price`, `discount_percentage`
- **Engagement:** `total_reviews`, `product_rating`, `purchased_last_month`
- **Marketplace Signals:** `is_best_seller`, `is_sponsored`, `has_coupon`, `buy_box_availability`
- **Logistics:** `delivery_date`, `sustainability_tags`
- **Metadata:** `product_image_url`, `product_page_url`, `data_collected_at`

**Data Notes:**
- Some columns dropped during preprocessing as not predictive (URLs, images)
- Contains both numeric and categorical features

---

### 3. **full_dataset_demand_predictions_xgb_optimized.csv** (Output Predictions)
**Size:** 1.3 MB | **Format:** CSV  
**Purpose:** Final predictions on the complete dataset.  
**Contains:**
- All original product features from `Amazon_Products.csv`
- **`predicted_demand`** column: Model output (log-transformed scale; use inverse transform `expm1()` for original scale)


---

### 4. **optimized_demand_model_xgb_tweedie.pkl** (Primary Trained Model)
**Size:** 9.7 MB | **Format:** Joblib/Pickle  
**Purpose:** Production-ready XGBoost model with Tweedie objective.  
**Model Details:**
- **Type:** XGBoost Regression with `objective='tweedie'` (handles heavy-tailed, skewed demand)

- **Performance:** Validation metrics (RMSE, MAE, R²) documented in notebook
- **Hyperparameters:** Tuned via GridSearch for optimal balance

