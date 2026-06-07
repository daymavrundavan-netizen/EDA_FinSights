# FinSight Bank Credit Risk Analytics & LGD Prediction Engine

## Project Overview
This repository contains the official end-to-end data processing, exploratory data analysis (EDA), and predictive modeling pipeline developed for FinSight Bank's retail lending portfolio. Built on a large-scale framework containing **2,000,000 unique records**, this analytics pipeline implements an integrated system to isolate the core drivers of non-performing assets (NPAs) and accurately predict Loss Given Default ($\text{LGD}\%$) on defaulted accounts.

The repository follows standard statistical modeling practices, handling complex data quality issues and structural multicollinearity, while validating classical ordinary least squares (OLS) assumptions and regularized machine learning configurations.

---

## Repository Deliverables
The submission is streamlined into two main artifacts for examiner review:
1. **`FinSight_Credit_Risk_Pipeline.ipynb`**: The live, fully executed Jupyter Notebook containing all data cleaning checkpoints, 12 mandatory portfolio charts with analytical captions, engineered metrics, and cross-validated modeling summaries.
2. **`FinSight_Executive_Summary.pdf`**: A board-ready, formal PDF report detailing actionable strategic risk recommendations and an executive summary of our empirical findings.

---

## Technical Pipeline & Methodology

### 1. Data Cleaning & Structured Imputation (Question 1)
* **Sequential Row Integration**: Consolidated nine separate relational tables on `loan_id` using progressive left merges, executing explicit row-count assertions to ensure zero data loss at a final baseline of 2,000,000 lines.
* **Anomalous Correction**: Identified eight injected data quality failures (e.g., negative incomes, impossible CIBIL inputs) and isolated them under a binary tracking flag (`dirty_flag`), applying column medians and modes to preserve total row count.
* **Missing Value Allocation**: Classified high-missing columns like `mths_since_last_delinq` as Missing Not at Random (MNAR) and verified successful resolution using pre-and-post null summation loops.
* **Variance Winsorization**: Treated severe right-tail skewness across the top six numeric attributes by capping extreme outliers at the 1st and 99th percentiles without dropping records.

### 2. Exploratory Data Analysis Takeaways (Question 2)
* **Underwriting Baseline**: Density modeling shows that past underwriting tightly favored prime credit tiers (CIBIL 650–750), though notable distributional overlap between performing and default statuses indicates that a single credit score cannot predict final losses on its own.
* **Pricing Monotonicity**: Grouped charting confirms that the bank’s internal grade system (A through G) correctly scales, demanding larger interest margins for progressively weaker risk tiers.
* **Portfolio Vulnerabilities**: Highlighted clear default concentrations within speculative loan purposes (e.g., small business funding) and specific high-risk geographic territories.
* **Macroeconomic Shocks**: Tracked the 2020 COVID-19 timeline spike to quantify the portfolio impact of economic lockdowns and calculated a structural lag between central bank repo rate hikes and active borrower default waves.

### 3. Accountable Feature Engineering (Question 3)
Twelve mandatory credit risk features were engineered, validated via `.describe()`, and correlated directly against `lgd_pct`:
* **Repayment Pressures**: `emi_to_income_ratio`, `loan_to_income_ratio`, `rate_spread_pct`, and `real_interest_rate`.
* **Bureau Dynamics**: `credit_util_composite`, `enq_velocity_score`, and a recency-weighted `delinq_severity_score` designed to track immediate financial distress over static historical metrics.
* **Asset Backing**: `income_stability_ratio`, `credit_depth_score`, and `collateral_coverage_ratio` (where high asset backing explicitly drives down final loss severity).
* **Log-Transformations**: Applied $\log(1 + x)$ transformations to log-annual income and loan amount to fix tail skewness and satisfy classical OLS normality criteria.

### 4. Regression Modeling & Diagnostics (Question 4)
* **Leakage Containment**: Restricted all regression passes strictly to the defaulted subset (`loan_status == 1`), removing all post-default indicators (e.g., recovery fees) to prevent target data leakage.
* **Multicollinearity Clean**: Calculated iterative Variance Inflation Factors ($\text{VIF}$) to drop overlapping metrics exceeding a threshold of 10, ensuring highly stable OLS parameters.
* **Machine Learning Regularization**: Evaluated OLS baselines alongside Ridge, Lasso, and ElasticNet models optimized via `GridSearchCV` with 5-fold cross-validation. The Lasso model successfully drove uninformative variables to absolute zero.
* **Assumption Diagnostics**: Generated a 4-panel diagnostic suite checking: (i) Residual Linearity, (ii) Error Normality (Q-Q Plot), (iii) Homoscedasticity (Scale-Location), and (iv) Cook's Distance to isolate influential outlier leverage.

---

## Strategic Policy Recommendations (Question 5)
1. **Enforce Hard EMI-to-Income Ceilings**: Automatically block loan approvals if an applicant's engineered `emi_to_income_ratio` exceeds 45% of verified monthly take-home pay to prevent structural over-leveraging.
2. **Adjust Risk Premiums for Lower Grades**: Apply a mandatory 250 basis point interest rate premium on lower-tier internal credit grades (Grades F and G) to cushion against elevated loss volatility.
3. **Speculative Exposure Restrictions**: Decrease maximum allowed Loan-to-Value (LTV) limits by 15% and mandate secondary co-signers for small business or speculative credit profiles.
4. **Deploy Dynamic Bureau Tracking**: Integrate the recency-weighted `delinq_severity_score` directly into the credit decision engine to adjust pre-approved credit lines if a borrower misses a payment within 90 days.
5. **Mandate Collateral Backing**: Require a minimum physical asset-backing ratio of 120% for high-exposure retail credit applications exceeding ₹25 Lakhs to safely manage the bank's write-off exposures.
