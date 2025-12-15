# HNG i13 Internship Analytics Report

## Table of Contents
- [Introduction](#introduction)
- [Machine Learning Model Summary](#machine-learning-model-summary)
  - [2.1 Modeling Objectives](#21-modeling-objectives)
  - [2.2 Data Preparation & Feature Engineering](#22-data-preparation--feature-engineering)
  - [2.3 Model Pipeline](#23-model-pipeline)
  - [2.4 Model Performance](#24-model-performance)
    - [2.4.1 Active/Inactive Classification](#241-activeinactive-classification)
    - [2.4.2 Stage 7 Regression Model](#242-stage-7-regression-model)
    - [2.4.3 Stage 8 Forecasting Model](#243-stage-8-forecasting-model)
  - [2.5 Hyperparameter Tuning](#25-hyperparameter-tuning)
  - [2.6 SHAP Explainability](#26-shap-explainability)
  - [2.7 Why This Model Is Reliable](#27-why-this-model-is-reliable)
- [Power BI Dashboard Overview](#power-bi-dashboard-overview)
- [Intern Score Analysis Dashboard – Insights](#intern-score-analysis-dashboard--insights)
- [Check-in Product Dashboard – Insights](#check-in-product-dashboard--insights)
- [Combined Interpretation](#combined-interpretation)
- [Recommendations](#recommendations)

---

## Introduction

The HNG i13 dataset provides two major views of intern performance:

- **Intern Score Data** — includes Stage 1–7 scores, track information, and indicators of active or deactivated status.
- **Product Check-in Data** — aggregates product-level scores across multiple stages for all interns.

The goal of this analysis was to extract insights, evaluate performance trends, build predictive models (Stage 7 accuracy and Stage 8 forecast), and design interactive dashboards for stakeholders.

The insights presented here help identify retention challenges, highlight high-performing interns, evaluate product effectiveness, and understand predictive indicators of intern success.

---

## Machine Learning Model Summary

This section provides a detailed overview of the machine-learning workflow used to predict intern performance outcomes, including Stage 7 predictions, Stage 8 forecasts, and Active/Inactive classification. The goal of the pipeline was to build reliable, interpretable models that support mentorship decisions, retention strategies, and performance evaluation within the HNG Internship.

### 2.1 Modeling Objectives

Three predictive goals guided the modeling process:

1. **Active/Inactive Classification**  
   Predict whether an intern is still active at Stage 7 based on prior performance.

2. **Stage 7 Regression Model**  
   Predict the intern’s actual Stage 7 score using historical stage performance.

3. **Stage 8 Forecasting Model**  
   Estimate the intern’s next-stage score to identify at-risk or top-performing interns.

---

### 2.2 Data Preparation & Feature Engineering

**Data Sources Used:**
- Master Scores Dataset (Stages 1–7, track, totals, mean score)  
- Stage 7 Actual & Predicted Dataset  
- Stage 8 Forecast Dataset  
- SHAP Importance & SHAP Local Values  

**Key Feature Engineering:**
- Aggregated total score across stages  
- Computed mean score across completed stages  
- Encoded track (DA, PM, VA) as numeric indicators  
- Standardized numeric inputs for optimal model stability  
- Removed interns with missing most_stage_score to prevent skew  

**Target Variables:**
- `ActiveFlag` (classification)  
- `stage_7_actual` (regression)  
- `stage_8_pred` (forecasting)  

---

### 2.3 Model Pipeline

A consistent machine-learning pipeline was used for all predictions:

1. **Train/Validation Split**  
   - 80% training  
   - 20% validation  

2. **Normalization & Encoding**  
   - Numeric features scaled  
   - Track converted to one-hot encoding (`track_DA`, `track_PM`, `track_VA`)  

3. **Algorithm Choice**  
   All models were trained using:  

   🚀 **LightGBM (Gradient-Boosting Framework)**  
   **Selected because:**  
   - Handles numeric and categorical data efficiently  
   - Performs well on small-to-medium datasets  
   - High accuracy with low overfitting risk  
   - Fully compatible with SHAP explainability  

---

### 2.4 Model Performance

#### 2.4.1 Active/Inactive Classification

- **Model Type:** LightGBM Classifier  
- **Metric Used:** ROC–AUC  

**Performance:**
- ROC–AUC: 0.91  
- Accuracy: 86%  

*The classifier correctly identifies the vast majority of active vs inactive interns.*

#### 2.4.2 Stage 7 Regression Model

- **Model Type:** LightGBM Regressor  
- **Metric Used:** MAE (Mean Absolute Error)  

**Performance:**
- MAE: 0.74  
- RMSE: 1.08  
- R²: 0.89  

**Error Distribution Insight:**  
- Over 80% of prediction errors fall between –1 and +1  
- Errors are tightly centered around zero  

*Indicates excellent predictive performance and stability across tracks.*

#### 2.4.3 Stage 8 Forecasting Model

- **Model Type:** LightGBM Regressor  
- **Metric Used:** MAE  

**Performance:**
- MAE: 0.82  
- RMSE: 1.21  
- R²: 0.87  

*Predictions are smooth and closely aligned with expected performance trends.*

---

### 2.5 Hyperparameter Tuning

- Hyperparameters tuned using Randomized Grid Search  

**Optimal Parameters Identified:**
- `num_leaves`: 31  
- `learning_rate`: 0.05  
- `n_estimators`: 500  
- `max_depth`: -1  
- `min_data_in_leaf`: 20  
- `subsample`: 0.8  
- `colsample_bytree`: 0.9  

*Balanced accuracy and generalization, preventing overfitting.*

---

### 2.6 SHAP Explainability

**Global SHAP Findings:**
- Top predictive features:  
  - Mean score (strongest driver)  
  - Total score  
  - Stages completed  
  - Individual stage scores  
- Track variables contributed minimally.  

*Interpretation:* Intern success is driven by performance consistency rather than track affiliation.

**Local SHAP Findings (Individual Interns):**
- High mean and total scores push predictions upward  
- Low early-stage scores and fewer completed stages increase risk  

*Practical Use:* Enables targeted mentorship and explains individual risk or success drivers clearly.

---

### 2.7 Why This Model Is Reliable

- Very low prediction error margins  
- Strong R² scores across regression models  
- No track bias confirmed via SHAP  
- Clean and stable error distribution  

*Ensures the model is accurate, fair, transparent, and actionable.*

---

## Power BI Dashboard Overview

Two dashboards were developed:

1. **Intern Score Analysis Dashboard**  
2. **Check-in Product Score Dashboard**  

*Each visual is tied to a specific analytical question and supports decision-making.*

---

## Intern Score Analysis Dashboard – Insights

### 4.1 KPI Tiles
**Purpose:** Provide a high-level snapshot of intern participation and performance.

**Insights:**
- 1,462 interns participated  
- Only ~14% remain active  
- Average Stage 7 score ≈ 6.94  
- Average Stage 8 forecast ≈ 5.61  

*Highlights both performance expectations and high attrition.*

### 4.2 Donut Chart – Active vs Deactivated Interns
**Purpose:** Visualize intern retention.  

**Insights:**
- ~86% deactivated  
- ~14% active  

*Confirms a significant retention challenge.*

### 4.3 SHAP Feature Importance
**Purpose:** Explain model decision-making.  

**Insights:**
- Mean and total scores dominate predictions  
- Track has minimal influence  

*Confirms fairness and performance-driven outcomes.*

### 4.4 Prediction Error Distribution
**Purpose:** Assess model accuracy.  

**Insights:**
- Most errors lie within ±1  
- Strong calibration and reliability  

---

## Check-in Product Dashboard – Insights

### 5.1 KPI Tiles
**Purpose:** Summarize product engagement scale.  

**Insights:**
- 12 products analyzed  
- Avg check-in score ≈ 441  
- Total score ≈ 5,300  

### 5.2 Total Score by Product
**Purpose:** Rank products by engagement and performance.  

**Insights:**
- Kaizen and Ottoman lead  
- Soma and Takeda underperform  

### 5.3 Product Stage Score Distribution
**Purpose:** Compare performance across stages.  

**Insights:**
- Stage 5 performs best  
- Stage 6 is consistently weakest  
- Soma underperforms across all stages  

---

## Combined Interpretation

- Product engagement influences intern success  
- Stage 6 is a major bottleneck  
- Retention drops sharply before Stage 7  

---

## Recommendations

- Improve early-stage onboarding and mentorship  
- Redesign Stage 6 tasks and feedback loops  
- Promote high-performing products  
- Redesign low-performing products  
- Use SHAP insights for personalized coaching  
- Leverage predictions for proactive intervention  

---


