# Ad Click Prediction with Cloud-Integrated Pipeline ☁️

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![Google Cloud](https://img.shields.io/badge/Google_BigQuery-Data_Warehouse-orange)

---
## Table of Contents

- [Executive Summary](#1-executive-summary)
- [Infrastructure & Tech Stack](#2-infrastructure--tech-stack)
- [Data Intelligence & Strategy](#3-data-intelligence--strategy)
- [Modelling Methodology](#4-modelling-methodology-in-progress)

---

## 1. Executive Summary
**Business Objective:**
In the competitive landscape of digital advertising, minimising wasted ad spend is paramount. This project aims to construct a machine learning pipeline capable of predicting user **Click-Through Rates (CTR)**. By identifying high-propensity user segments, the model enables more efficient budget allocation and targeted marketing strategies.

**Technical Strategy:**
Unlike standard static analysis, this project simulates a scalable **Modern Data Stack** workflow. Raw data is ingested into a cloud data warehouse (**Google BigQuery**), processed via a Python-based ETL pipeline, and modelled using advanced gradient boosting techniques.

---

## 2. Infrastructure & Tech Stack
The project is built on a cloud-first architecture to ensure scalability and reproducibility.

* **Data Warehouse:** Google BigQuery (Sandbox Environment)
* **ETL Pipeline:** Python (`google-cloud-bigquery`) for secure data extraction and transformation.
* **Modelling:** Scikit-Learn & XGBoost (Planned).
* **Explainability:** SHAP (SHapley Additive exPlanations) for model transparency.
* **Authentication:** Service Account security with environment variable management.

### 🏗️ Cloud Integration Evidence
*The following execution plan demonstrates the SQL query performance within the BigQuery console, confirming data integrity before extraction.*

```sql
/*
 * QUERY: Initial Data Quality Check
 * OBJECTIVE: Assess dataset balance and identify potential null values before ETL.
 * AUTHOR: Chien-Hao Wang
 */

SELECT
    COUNT(*) AS total_impressions,
    COUNTIF(click = 1) AS total_clicks,
    ROUND(COUNTIF(click = 1) / COUNT(*) * 100, 2) AS ctr_percentage,
    COUNTIF(age IS NULL) AS missing_age_records,
    COUNTIF(gender IS NULL) AS mssing_gender_records
FROM
    `marketing_data.ad_clicks_raw`;
```

---

## 3. Data Intelligence & Strategy

### A. Dataset Characteristics
* **Source:** Multi-channel digital advertising impressions.
* **Volume:** 10,000 records.
* **Baseline Performance:** The dataset exhibits a high baseline CTR of **65%**, suggesting a retargeting campaign context or a pre-qualified audience segment.

### B. Strategic Handling of "Missingness"
A critical challenge identified during the Exploratory Data Analysis (EDA) was significant data missingness in demographic fields.

| Feature | Missing Rate | Handling Strategy |
| :--- | :--- | :--- |
| **Age** | 47.7% | **Imputation:** Utilising KNN/Median imputation to preserve distribution. |
| **Gender** | 46.9% | **Explicit Labelling:** Categorised as "Unknown" to treat missingness as a signal. |
| **Device Type** | 20.0% | **Explicit Labelling:** Categorised as "Unknown". |

**Validation of Strategy:**
A "Missingness Bias Analysis" was conducted to determine if data was *Missing Not At Random (MNAR)*.
* **Observation:** The CTR for records *with* missing age (66.07%) is statistically comparable to those *without* (64.02%).
* **Conclusion:** The missing data is likely **Missing at Random (MAR)**. Therefore, dropping these rows would result in unnecessary information loss. The chosen imputation and labelling strategy preserves 100% of the dataset for modelling.

### C. Behavioural Insights
* **Content Context Matters:** Analysis reveals that user intent significantly impacts engagement. Users browsing **Entertainment** content exhibit the highest click-through rate (**68.8%**), whereas those reading **News** show the lowest (**59.1%**). This suggests that users are more receptive to ads when in a leisure mindset compared to an information-seeking one.
* **Temporal Patterns:** Ad performance peaks during the **Afternoon (CTR 68.6%)** and drops to its lowest at **Night (62.5%)**, indicating that budget allocation should be weighted towards daytime slots.

<p align="center">
  <img src="charts/ctr_distribution.png" width="900">
  <br>
  <em>Figure 1: Distribution of Target Variable showing Class Balance</em>
</p>

<p align="center">
  <img src="charts/categorical_click_rates.png" width="900">
  <br>
  <em>Figure 2: Click Rate variance across Browsing History and Time of Day</em>
</p>

---

## 4. Modelling Methodology (In Progress)
The predictive modelling phase focuses on maximizing the **F1-Score** to balance precision and recall, ensuring we capture potential clickers without overspending on false positives.

* **Algorithm Selection:**
    * **Logistic Regression:** Established as a baseline for interpretability.
    * **XGBoost Classifier:** Selected for its superior performance on tabular data and ability to handle non-linear relationships.
* **Evaluation Metrics:** Accuracy, Precision, Recall, ROC-AUC.
* **Model Interpretability:** Utilising SHAP values to explain *why* the model predicts a specific user will click, providing actionable insights for marketing teams.

*[Model Performance Metrics and Confusion Matrix to be added upon training completion]*

