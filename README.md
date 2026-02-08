# Churn Prediction Feature Engineering Pipeline

This repository demonstrates an end-to-end feature engineering and machine learning workflow for churn prediction using **Databricks Feature Engineering** with declarative APIs.

##  Overview

This project showcases how to:
* Define data sources with entity and timeseries columns
* Create features using multiple aggregation functions and window types
* Materialize features to offline stores with scheduling
* Generate point-in-time correct training data
* Train a churn prediction model with feature lineage
* Perform batch inference with automatic feature computation

##  Project Structure

```
feature_store/
├── README.md                                    # This file
├── 00_synthetic_data_generation                 # Generates synthetic data for demo
└── 01_churn_prediction_feature_pipeline         # Main feature engineering pipeline
```

##  Data Model

The pipeline uses the following synthetic data tables:

* **user_tenant_mapping** - User and tenant relationships
* **user_click_events** - User engagement events (clicks, sessions)
* **confluence_events** - Collaboration activity (page views, comments)
* **jira_events** - Issue tracking activity (reactions, activities)
* **tenant_metrics** - Tenant-level aggregated metrics
* **churn_labels** - Target labels for training

##  Getting Started

### Prerequisites

* Databricks workspace with Unity Catalog enabled
* Serverless or interactive cluster
* Access to create catalogs and schemas

### Setup

1. **Configure Parameters**: The notebooks use query parameters (widgets) for configuration:
   * `catalog` - Unity Catalog catalog name (default: `ananyaroy`)
   * `schema` - Schema for feature tables (default: `fs_demo`)
   * `source_schema` - Schema containing source data (default: `feature_store`)

2. **Generate Synthetic Data**: Run `00_synthetic_data_generation` notebook first to create sample data

3. **Run Feature Pipeline**: Execute `01_churn_prediction_feature_pipeline` to:
   * Define data sources
   * Create 24+ features across multiple categories
   * Train a churn prediction model
   * Perform batch scoring

##  Features Created

The pipeline creates **24 features** across multiple categories:

### User Engagement Features (5)
* Click counts (7-day, 30-day windows)
* Session duration (average 7-day, total 30-day)
* Product diversity (30-day unique products)

### Confluence Collaboration Features (6)
* Dwell time (14-day, average 30-day)
* Comments (30-day count)
* Unique pages visited (30-day)
* Lagged metrics (7-day, 30-day with offset)

### Jira Activity Features (5)
* Weekly activities (tumbling window)
* Reaction statistics (max, min, total)
* Unique issues (monthly)

### Filtered Features (2)
* JSW-specific activities
* Bug-related activities

### Statistical Features (3)
* Session duration variance
* Confluence dwell variance
* Jira reaction variance

### Tenant-Level Features (3)
* Average key events
* Max active users
* Total linked artifacts

##  Key Technologies

* **Databricks Feature Engineering** - Declarative feature creation and management
* **Unity Catalog** - Centralized governance and lineage
* **MLflow** - Model tracking and registry
* **PySpark** - Distributed data processing
* **scikit-learn** - Model training (Gradient Boosting Classifier)

##  Window Types Used

### SlidingWindow
* **Pattern**: Overlapping rolling windows
* **Use Case**: Trend analysis, smooth metrics
* **Example**: "Clicks in last 7 days" (slides daily)

### TumblingWindow
* **Pattern**: Non-overlapping fixed windows
* **Use Case**: Periodic reports, discrete buckets
* **Example**: "Weekly activity count"

### ContinuousWindow
* **Pattern**: Lookback from event time with offset
* **Use Case**: Point-in-time aggregations with lag
* **Example**: "Events in last 30 days with 1-day lag"

##  Key Concepts

### Point-in-Time Correctness
Features are computed using only data available at the observation timestamp, preventing data leakage and enabling accurate backtesting.

### Feature Lineage
Models are logged with complete feature lineage, tracking which features were used and how they were computed from source tables.

### Batch Scoring
The `score_batch()` API automatically:
1. Loads the model and its feature dependencies
2. Computes features from source tables (point-in-time correct)
3. Applies the model
4. Returns predictions with all features

##  Model Performance

The trained Gradient Boosting Classifier achieves:
* ROC-AUC Score: ~0.85-0.90 (on synthetic data)
* Feature importance analysis included
* Classification report with precision/recall metrics

##  Resources

* [Databricks Feature Engineering Documentation](https://docs.databricks.com/machine-learning/feature-store/index.html)
* [Unity Catalog Feature Engineering](https://docs.databricks.com/en/machine-learning/feature-store/uc/feature-tables-uc.html)
* [MLflow Model Registry](https://docs.databricks.com/mlflow/model-registry.html)

##  Notes

* This is a demonstration project using synthetic data
* Feature values and model performance are for illustration purposes
* Adapt the feature definitions and model architecture for your specific use case

##  Contributing

This is a demo repository. Feel free to adapt and extend for your own use cases.

---

**Last Updated**: February 2026  
**Databricks Runtime**: Compatible with DBR 14.3 LTS and above