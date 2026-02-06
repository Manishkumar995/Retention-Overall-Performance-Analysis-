📌 Project: Retention Analysis Dashboard

**1. Project Summary**

The Retention Analysis Dashboard was designed to track and improve customer retention and policy renewals. The goal was to identify churn risks, monitor customer engagement trends, and provide actionable insights to the business team for targeted retention strategies.

By combining Power BI with Excel data sources, the dashboard automated weekly reporting, provided real-time visibility into customer behavior, and reduced reporting efforts significantly.

**2. Steps Used**
**Step 1: Business Understanding**

**Objective: Measure policy retention performance and identify customer segments at risk of churn.**

**Key KPIs:**

- Renewal Rate (%)

- Churn Rate (%)

- Customer Tenure

- Retention by Product, Channel, and Customer Segment

- RFM Segmentation (Recency, Frequency, Monetary value)

**Step 2: Data Collection & Preparation**

Imported policy and customer data from Excel spreadsheets into Power BI.

Cleaned and shaped data using Power Query:

Removed duplicates, nulls, and formatting errors.

Standardized product/channel names.

Created a Calendar table for time-based analysis.
Customer Retention Rate (Core KPI)

**Definition
Customers active at end of period ÷ customers active at start of period**

``` WITH base AS (
    SELECT
        customer_id,
        MIN(transaction_date) AS first_txn,
        MAX(transaction_date) AS last_txn
    FROM transactions
    GROUP BY customer_id
)
SELECT
    COUNT(CASE WHEN last_txn >= DATE '2024-12-31' THEN 1 END) * 1.0 /
    COUNT(CASE WHEN first_txn <= DATE '2024-01-01' THEN 1 END) AS retention_rate
FROM base;
```


** Monthly Retention (Cohort Analysis)**

```
WITH cohorts AS (
    SELECT
        customer_id,
        DATE_TRUNC('month', MIN(transaction_date)) AS cohort_month
    FROM transactions
    GROUP BY customer_id
),
activity AS (
    SELECT
        t.customer_id,
        DATE_TRUNC('month', t.transaction_date) AS activity_month
    FROM transactions t
)
SELECT
    c.cohort_month,
    a.activity_month,
    COUNT(DISTINCT a.customer_id) AS active_customers
FROM cohorts c
JOIN activity a
    ON c.customer_id = a.customer_id
GROUP BY c.cohort_month, a.activity_month
ORDER BY c.cohort_month, a.activity_month;
```
**Step 3: Data Modeling**

**Designed a star schema with:**

Fact Table → Policy transactions (renewals, cancellations, tenure, premiums).

Dimension Tables → Customer, Product, Channel, Date.

Established one-to-many relationships for drill-down reporting.

**Step 4: DAX Calculations (Measures)** =

**Key DAX measures included:**

**Renewal Rate** = DIVIDE([Renewals], [Total Policies])

**Churn Rate** = 1 - [Renewal Rate]

**Customer Tenure (Months)** = DATEDIFF(Customer[StartDate], TODAY(), MONTH)

**RFM Segmentation:
** 
Recency → Days since last renewal

Frequency → Number of renewals per customer

Monetary → Total premium value

These measures allowed segmentation of high-value but high-risk customers.

**Step 5: Dashboard Design**

**KPI Section (cards):** Renewal Rate, Churn %, Average Tenure, Active Customers.

**Trend Charts:** Renewal & churn rate by month.

**Bar Charts:** Retention by product line, channel, and geography.

**Customer Segmentation**: RFM analysis to identify loyal customers vs. churn risks.

**Drill-through Pages**: Customer-level view showing renewal history.

Interactive slicers for filtering by region, product, or policy year.

**Step 6: Enhancements & Automation**

Automated refresh connected to Excel data sources.

Added conditional formatting (e.g., churn > 20% highlighted in red).

Reduced reporting time by 40% by replacing manual Excel reports with Power BI automation.

**3. Results / Outcomes**

Delivered an interactive Retention Analysis Dashboard in Power BI.

Provided business leaders with real-time visibility into:

Customer churn trends.

Renewal performance by product, region, and channel.

High-value churn-risk customers for targeted retention campaigns.

**Business Impact:**

- Reduced manual reporting effort by 40%.

- Enabled data-driven retention strategies that improved policy retention by 14%.

- Helped sales & operations teams proactively intervene with at-risk customers.
