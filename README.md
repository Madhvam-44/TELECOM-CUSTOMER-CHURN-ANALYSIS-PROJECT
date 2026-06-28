# TELECOM-CUSTOMER-CHURN-ANALYSIS-PROJECT
# Telecom Customer Churn Analysis & Prediction

An end-to-end churn analytics project combining **SQL Server** (data cleaning & ETL), Power BI (interactive dashboarding), and Python / scikit-learn (predictive modeling) to understand why customers churn and who is likely to churn next.


1. Tech Stack

- SQL Server (T-SQL)** — data staging, cleaning, views
- Power BI — interactive 2-page dashboard
- Python — pandas, scikit-learn, seaborn, matplotlib
- Excel — data exchange layer between SQL views and the Python notebook


2. Project Workflow

1. Raw data loaded into 'stg_Churn' (staging table)
2. Data exploration — distinct value checks on Gender, Contract, State, Internet Type; revenue split by Customer_Status
3. Null audit across all 31 columns
4. Cleaning — nulls handled with business logic, not blanket fills:
   - ISNULL(Value_Deal, 'None')
   - ISNULL(Online_Security, 'No')
   - ISNULL(Churn_Reason, 'Others')
5. Cleaned data loaded into 'prod_Churn' (production table)
6. Two views created:
   - 'vw_ChurnData' — status = Churned or Stayed → used to train the ML model
   - 'vw_JoinData'— status = Joined → new customers scored for future risk
7. Power BI connects to 'prod_Churn' for the descriptive Summary page
8. Python notebook reads the views, encodes categorical fields, trains a Random Forest, evaluates it, then scores 'vw_JoinData' and exports at-risk customers to 'Predictions.csv'
 

3. Repository Contents

- `SQLQuerychurn_analysis_prjct.sql` — full ETL script (staging, exploration, cleaning, load, views)
- `Churn_Prediction.ipynb` — ML notebook (preprocessing, training, evaluation, scoring)
- `Churn_Analysis_pic1.png` — Dashboard Page 1 screenshot (Summary)
- `Churn_Prediction.png` — Dashboard Page 2 screenshot (Prediction)


4. Dashboard Pages

Page 1 — Churn Analysis: Summary
A descriptive, historical view of churn across the customer base.
![image alt](https://github.com/Madhvam-44/TELECOM-CUSTOMER-CHURN-ANALYSIS-PROJECT/blob/main/IMAGES/Churn%20Analysis.png)


--Top KPIs

- Total Customers: 6,418
- New Joiners: 411
- Total Churn: 1,732
- Churn Rate: 27.0%

--Breakdowns included

- Churn by Gender, Age Group, Tenure Group
- Churn Rate by Contract type and Payment Method
- Top 5 states by churn rate
- Churn by Internet Type
- Per-service churn % (Online Security, Online Backup, Streaming, Device Protection, etc.)
- Churn Distribution by Category — Competitor, Attitude, Dissatisfaction, Price, Other
- Slicers: Married status, Monthly Charge

Purpose: This page gives stakeholders a clear, at-a-glance diagnosis of where churn is concentrated and what's driving it  across customer demographics, contract types, services, and geography. It serves as the data-backed starting point for any retention strategy, helping the business prioritize where to act instead of guessing.

Page 2 — Churn Analysis: Prediction
A forward-looking view powered by the Random Forest model, applied to customers who recently joined and have no churn outcome yet.
![image alt](https://github.com/Madhvam-44/TELECOM-CUSTOMER-CHURN-ANALYSIS-PROJECT/blob/main/IMAGES/Churn%20Predictions.png)

--Headline metric
- Predicted Churners: 378

-- Breakdowns of the predicted at-risk segment

- Gender split — 246 Female / 132 Male
- Age Group, Marital Status, Tenure Group
- Payment Method, Contract type
- State-wise distribution of at-risk customers
- Detail table per flagged customer: Customer_ID, Monthly_Charge, Total_Revenue, Total_Refunds, Number_of_Referrals

**Purpose:**  This page takes the analysis a step further by predicting which active customers are likely to churn next, before they actually leave turning a reactive problem into a proactive one. It hands the business a ready-to-use, prioritized customer list so retention teams know exactly who to target and how much revenue is at stake.



5. Machine Learning Model

**Setup**
- Algorithm: `RandomForestClassifier(n_estimators=100, random_state=42)`
- Features: 28 customer attributes (demographics, subscribed services, billing, contract info); categorical fields label-encoded
- Target: `Customer_Status` → Stayed = 0, Churned = 1
- Train/Test split: 80% / 20%
- Evaluation set size: 1,202 customers

**Performance**
- Accuracy: 0.84
- Stayed (0): Precision 0.86, Recall 0.92, F1 0.89
- Churned (1): Precision 0.78, Recall 0.65, F1 0.71

**Process notes**
- Feature importances plotted to flag low-impact predictors (importance ≈ 0.01) for possible removal
- Model applied to `vw_JoinData` (unlabeled new joiners) to predict future churn risk
- Customers predicted as "Churned" exported to `Predictions.csv` — this feeds the at-risk table on the Prediction dashboard page



6. Key Insights

1. Overall churn rate: **27.0%**
2. Customers over age 50 churn the most of any age band (31.0%)
3. Contract type is the strongest churn lever:
   - Month-to-month: 46.5% churn
   - One-year: 11.0% churn
   - Two-year: 2.7% churn
4. Competitor activity is the top churn category (761 customers), ahead of Dissatisfaction (300) and Attitude (301)
5. Fiber optic users churn more (41.1%) than Cable (25.7%) or DSL (19.4%)
6. Jammu & Kashmir has the highest churn rate among the top 5 states (57.2%)


7. How to Reproduce

1. Run `SQLQuerychurn_analysis_prjct.sql` against SQL Server to build `stg_Churn` → `prod_Churn` and the two views
2. Export `vw_ChurnData` and `vw_JoinData` to Excel (or connect directly via a SQL connector)
3. Open `Churn_Prediction.ipynb`, update the file paths, and run all cells to train and evaluate the model
4. Connect Power BI Desktop to `prod_Churn` / the views to build or refresh the dashboard

