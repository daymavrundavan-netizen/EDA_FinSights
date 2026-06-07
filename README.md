# FinSight Bank Credit Risk Analytics & LGD Prediction Engine

## Project Overview
[cite_start]This repository contains the official end-to-end data processing, exploratory analysis, and predictive modeling pipeline developed for FinSight Bank's retail lending portfolio[cite: 16, 59]. [cite_start]Built on a scale of **2,000,000 unique records**, this analytics suite implements an integrated framework to isolate the core drivers of non-performing assets (NPAs) and accurately predict Loss Given Default ($\text{LGD}\%$) on defaulted accounts[cite: 19, 46].

[cite_start]The pipeline adheres strictly to classical linear regression assumptions and regularization constraints, handling complex data quality issues and structural multicollinearity entirely within python[cite: 12, 14, 48].

---

## Submission Artifacts
[cite_start]This repository is streamlined into two core deliverables for examiner review:
1. [cite_start]**`FinSight_Credit_Risk_Pipeline.ipynb`**: The live, fully executed Jupyter Notebook containing all data cleaning checkpoints, 12 mandatory portfolio charts with analytical captions, engineered metrics, and cross-validated modeling summaries[cite: 10, 11, 24, 39, 50].
2. [cite_start]**`FinSight_Executive_Summary.pdf`**: A board-ready, formal PDF report detailing actionable strategic risk recommendations and a summary of our empirical findings[cite: 53].

---

## Operational Pipeline & Methodology

### 1. Data Cleaning & Structured Imputation (Question 1)
* [cite_start]**Sequential Row Integration**: Consolidated nine separate relational tables on `loan_id` using progressive left merges, executing explicit row-count assertions to ensure zero data loss at a final baseline of 2,000,000 lines[cite: 16, 19].
* [cite_start]**Anomalous Correction**: Identified eight injected data quality failures (e.g., negative incomes, impossible CIBIL inputs) and isolated them under a binary tracking flag (`dirty_flag`), applying column medians and modes to preserve total row count[cite: 12, 20].
* [cite_start]**Missing Value Allocation**: Classified high-missing columns like `mths_since_last_delinq` as Missing Not at Random (MNAR) and verified successful resolution using pre-and-post null summation loops[cite: 21].
* [cite_start]**Variance Winsorization**: Treated severe right-tail skewness across the top six numeric attributes by capping extreme outliers at the 1st and 99th percentiles without dropping records[cite: 12, 22].

### 2. Exploratory Data Analysis Takeaways (Question 2)
* **Underwriting Baseline**: Density modeling shows that past underwriting tightly favored prime credit tiers (CIBIL 650–750), though notable distributional overlap between performing and default statuses indicates that a single credit score cannot predict final losses on its own[cite: 27].
* [cite_start]**Pricing Monotonicity**: Grouped charting confirms that the bank’s internal grade system (A through G) correctly scales, demanding larger interest margins for progressively weaker risk tiers[cite: 31].
* [cite_start]**Portfolio Vulnerabilities**: Highlighted clear default concentrations within speculative loan purposes (e.g., small business funding) and specific high-risk geographic territories[cite: 32, 33].
* **Macroeconomic Shocks**: Tracked the 2020 COVID-19 timeline spike to quantify the portfolio impact of economic lockdowns and calculated a structural lag between central bank repo rate hikes and active borrower default waves[cite: 34, 35].

### 3. Accountable Feature Engineering (Question 3)
Twelve mandatory credit risk features were engineered, validated via `.describe()`, and correlated directly against `lgd_pct`[cite: 39]:
* [cite_start]**Repayment Pressures**: `emi_to_income_ratio`, `loan_to_income_ratio`, `rate_spread_pct`, and `real_interest_rate`[cite: 40].
* [cite_start]**Bureau Dynamics**: `credit_util_composite`, `enq_velocity_score`, and a recency-weighted `delinq_severity_score` designed to track immediate financial distress over static historical metrics[cite: 41].
* **Asset Backing**: `income_stability_ratio`, `credit_depth_score`, and `collateral_coverage_ratio` (where high asset backing explicitly drives down final loss severity)[cite: 42].
* [cite_start]**Log-Transformations**: Applied $\log(1 + x)$ transformations to log-annual income and loan amount to fix tail skewness and satisfy classical OLS normality criteria[cite: 43].

### 4. Regression Modeling & Diagnostics (Question 4)
* [cite_start]**Leakage Containment**: Restricted all regression passes strictly to the defaulted subset (`loan_status == 1`), removing all post-default indicators (e.g., recovery fees) to prevent target data leakage[cite: 46, 47].
* [cite_start]**Multicollinearity Clean**: Calculated iterative Variance Inflation Factors ($\text{VIF}$) to drop overlapping metrics exceeding a threshold of 10, ensuring highly stable OLS parameters[cite: 48].
* **Machine Learning Regularization**: Evaluated OLS baselines alongside Ridge, Lasso, and ElasticNet models optimized via `GridSearchCV` with 5-fold cross-validation[cite: 14, 49, 50]. The Lasso model successfully drove uninformative variables to absolute zero[cite: 50].
* [cite_start]**Assumption Diagnostics**: Generated a 4-panel diagnostic suite checking: (i) Residual Linearity, (ii) Error Normality (Q-Q Plot), (iii) Homoscedasticity (Scale-Location), and (iv) Cook's Distance to isolate influential outlier leverage[cite: 51].

---

## Strategic Policy Recommendations (Question 5)
1. [cite_start]**Dynamic Interest Capping**: Implement an automatic 250 basis point risk premium baseline on weak internal loan grades (Grades F and G) to cushion against default volatility[cite: 31, 54].
2. [cite_start]**Fixed Obligation Caps**: Enforce a hard automated block on loan approvals if a mid-tier borrower's engineered `emi_to_income_ratio` exceeds 45% of verified monthly take-home pay[cite: 40, 54].
3. [cite_start]**Speculative Exposure Restrictions**: Decrease maximum allowed Loan-to-Value (LTV) limits by 15% and mandate secondary co-signers for small business or speculative credit profiles[cite: 32, 54].
4. [cite_start]**Deploy Dynamic Bureau Tracking**: Integrate the recency-weighted `delinq_severity_score` directly into the credit decision engine to adjust pre-approved credit lines if a borrower misses a payment within 90 days[cite: 41, 54].
5. [cite_start]**Mandate Collateral Backing**: Require a minimum physical asset-backing ratio of 120% for high-exposure retail credit applications exceeding ₹25 Lakhs to safely manage the bank's write-off exposures[cite: 42, 54].
