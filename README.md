9. Feature Engineering and Modeling (Detailed Write-up)
9.1 Feature Engineering
What was done
The dataset was copied into a feature-engineering version and transformed into a modeling-ready structure. Numerical columns were explicitly converted to numeric types to avoid hidden type issues.

New domain-informed features were created:

Discount behavior features:
Discount ratio
Price competitiveness
Delivery/availability feature:
Availability lag in days between collection time and delivery date
Review and trust features:
Log reviews
Review social proof
Rating confidence
Review popularity
Promotion impact features:
Deal appeal (discount signal × coupon signal)
Promotion strength (combined coupon/sponsored/best-seller effect)
Category/position features:
Category count
Category price rank
A redundant discount column was dropped after deriving stronger engineered price-discount signals.
The target variable was set as purchased_last_month, and final predictor matrix X and target y were prepared.

Why this was done
Raw e-commerce variables are often insufficient to capture buying behavior. Demand is influenced by interactions, not single fields.
For example:

A discount matters more when combined with coupon visibility.
Rating quality matters more when supported by review volume.
Delivery speed affects conversion as an operational trust signal.
Feature engineering converts raw attributes into behavioral signals that machine learning models can use more effectively.

Result interpretation
This step shifts the project from descriptive analysis to predictive modeling readiness.
The transformed features now encode price psychology, social proof, delivery convenience, and promotional pressure, which are all realistic demand drivers in online marketplaces.

Key takeaways
The feature space became more meaningful than the original raw columns.
Business logic was encoded directly into model inputs.
This is likely one of the strongest contributors to model performance gains.
9.2 Correlation-Based Feature Reduction
What was done
A correlation matrix was computed for numeric input features.
Any feature pair with absolute correlation above 0.95 was treated as redundant, and one of the features was dropped.

Why this was done
Highly correlated features add duplicate information. This can:

Increase model complexity without adding predictive value
Introduce instability in some model families
Make interpretation less clear
Removing collinear features simplifies the feature set and improves robustness.

Result interpretation
The notebook prints:

Input feature count before reduction
Number of dropped features
Names of dropped features
Final feature count after reduction
If many features were dropped, the engineered set had substantial overlap.
If few features were dropped, the engineered set was largely complementary.

Key takeaways
Redundancy was reduced while preserving signal.
The final predictor matrix became leaner and cleaner.
Reduction was correctly applied to inputs only, not to the target.
9.3 PCA-Based Reduction Note
What was done
The markdown indicates PCA-based further reduction as a planned step after correlation filtering.
In the visible execution flow, correlation-based reduction and scaling are clearly implemented, while PCA usage is not visibly finalized in the shown section.

Why this matters
Documentation should align with execution.
If PCA is applied in another cell, it should be explicitly reported with explained variance and retained components.

Result interpretation
The existing pipeline remains valid without PCA, especially because tree-based models (XGBoost, Random Forest, CatBoost, LightGBM) typically do not require PCA to perform well.

Key takeaways
Correlation filtering is fully implemented.
PCA should be reported only if explicitly executed and used in final training.
10. Normality, Transformation, Scaling, and Re-check
What was done
A complete preprocessing block was applied:

Identified numeric and non-numeric columns
Filled missing numeric values with median
Computed skewness before transformation
Applied log1p to highly right-skewed non-negative numeric columns (threshold > 1.0)
Standardized numeric features with StandardScaler
Preserved non-numeric features unchanged (if present)
Saved scaler parameters to scaler_weights_demo.csv
Recomputed skewness after transformation and scaling
Why this was done
Demand data and review-related variables are usually right-skewed.
Skew can distort training and make models less stable.
Log transformation reduces extreme-value dominance, and scaling makes feature magnitudes comparable.

Saving scaler statistics supports reproducibility and later deployment.

Result interpretation
The before-versus-after skewness outputs should show reduced skew concentration.
Some residual skew is acceptable in marketplace data, but major extremes should be softened.

Key takeaways
The preprocessing sequence is methodologically strong: diagnose, transform, scale, verify.
Model inputs became more stable and consistent.
Reproducibility improved through saved scaler parameters.
11. Model Testing and Evaluation (Benchmark Stage)
What was done
Four models were trained and compared:

XGBoost Tweedie
LightGBM
Random Forest
CatBoost
Pipeline steps:

Train-test split
Train each model on the processed feature set
Evaluate each using a unified metric function
Select the best model by RMSE
Save the best model
Generate full-dataset predictions and absolute errors
Metrics reported:

MAE
RMSE
RMSE as percent of mean demand
WAPE
R²
Median Absolute Error
RMSE on log1p scale
Tail RMSE for top-demand products (p90+)
Why this was done
No single metric fully describes model quality.
Using both central and tail-focused metrics ensures that:

Average-case prediction is accurate
High-demand products (business-critical cases) are not ignored
Comparing multiple strong ensemble models reduces model-selection risk.

Result interpretation
General interpretation rules:

Lower MAE, RMSE, WAPE = better absolute accuracy
Higher R² = better explained variance
Lower Tail RMSE = better performance on high-demand products
Selecting by RMSE gives a balanced model, but tail behavior should still influence final business choice.

Key takeaways
Evaluation is comprehensive and business-aware.
The project goes beyond academic scoring by evaluating high-demand tail risk.
Artifact saving (model and predictions) supports downstream analysis and deployment.
12. Optimized Final Model Training (XGBoost Tweedie)
What was done
After benchmark comparison, an optimized XGBoost Tweedie model was trained with tuned parameters:

Larger number of estimators
Lower learning rate
Controlled depth and regularization
Subsampling and column sampling
Tweedie objective for non-negative skewed demand
Additional enhancement:

Sample weights were used as sqrt(y + 1) to give more learning attention to higher-demand samples.
Outputs saved:

Full-dataset predictions file:
full_dataset_demand_predictions_xgb_optimized.csv
Final optimized model:
optimized_demand_model_xgb_tweedie.pkl
Why this was done
The target is non-negative and skewed, so Tweedie objective is suitable.
Weighted training helps reduce underfitting on high-demand observations, which often drive major business value.

Result interpretation
If this optimized model improved RMSE/WAPE and especially Tail RMSE versus baseline benchmark models, tuning and weighting were successful.
Even when average metrics improve modestly, tail improvement is highly valuable for real demand planning.

Key takeaways
The final stage is production-oriented, not just experimental.
Model design is aligned with demand distribution characteristics.
Saved outputs make the pipeline deployment-ready and auditable.
Final Combined Conclusion (From Feature Engineering Onward)
What this entire block achieved
Converted raw marketplace data into behavior-focused predictive signals
Reduced multicollinearity and feature redundancy
Stabilized data distribution via skew correction and scaling
Benchmarked multiple advanced regressors with robust metrics
Finalized and exported an optimized XGBoost Tweedie demand model
Practical business interpretation
The modeling pipeline suggests demand is influenced by a combination of:

Price competitiveness and discount structure
Social proof and trust signals
Promotion strength and platform visibility mechanics
Availability and delivery factors
This confirms that demand prediction in e-commerce is multi-factorial and non-linear, and your modeling approach is appropriately designed for that reality.

Overall key takeaways
Feature engineering quality is the foundation of your model performance.
Tail-aware evaluation is a major strength of this project.
The final outputs are ready for report use, further diagnostics, and deployment extensions.