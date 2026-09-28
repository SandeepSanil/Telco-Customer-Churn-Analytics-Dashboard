# ChurnScope | Telco Customer Churn Analytics Dashboard

An interactive, dark-themed **Power BI** dashboard that analyzes customer churn for a telecom company: who is leaving, why, and how much revenue is at risk.

## Project Overview

Customer churn is one of the costliest problems for subscription businesses. This project takes 7,043 telecom customer records, cleans and models them, and turns them into a 3-page dashboard that answers three business questions:

1. **How big is the churn problem?** (Overview)
2. **Which customer segments churn the most?** (Segments)
3. **Which services and billing choices drive churn?** (Services & Billing)

## Key Findings

| Metric | Value |
|---|---|
| Total customers | 7,043 |
| Churned customers | 1,869 |
| **Overall churn rate** | **26.5%** |
| Monthly revenue at risk | ~139.1K (about 30.5% of monthly revenue) |
| Average tenure | 32.4 months |

**Where churn is highest**

- **Contract type:** month-to-month customers churn at **42.7%**, versus 11.3% on one-year and just **2.8%** on two-year contracts.
- **Tenure:** customers in their first 12 months churn at **47.4%**, falling to 9.5% after 49 months.
- **Internet service:** fiber optic customers churn at **41.9%**, versus 19.0% for DSL and 7.4% for customers without internet.
- **Payment method:** electronic check users churn at **45.3%**, versus 15 to 19% for other methods.
- **Demographics:** senior citizens churn at 41.7% (vs 23.6%); customers without a partner (33.0%) or dependents (31.3%) churn more than those with them. Gender has almost no effect (26.9% vs 26.2%).
- **Support services:** customers without tech support (31.2%) or online security (31.3%) churn roughly twice as often as those with them (15.2% and 14.6%).
- **Price:** churn rises with monthly charges: 10.9% (low), 23.9% (medium), 35.4% (high).
- **Highest-risk combination:** month-to-month contract with fiber optic internet churns at **54.6%**.

## Dashboard Pages

| Page | What it shows |
|---|---|
| **Overview** | KPI cards, churned vs retained split, churn rate by contract, tenure group and payment method |
| **Segments** | Churn by senior citizen, partner, dependents and gender; churned vs retained by contract; decomposition tree of churned customers |
| **Services & Billing** | Revenue and revenue at risk, churn by internet service, tech support and online security, contract x internet service heatmap matrix, churn by charge band, tenure vs monthly charges scatter |


**Interactivity:** slicers (Contract, Internet Service, Senior Citizen, Tenure Group, Charge Band, Payment Method), cross-filtering between visuals, page navigation buttons, and a reset-filters bookmark.

## Data

- **Source:** IBM Telco Customer Churn dataset (`WA_Fn-UseC_-Telco-Customer-Churn.csv`), available on Kaggle.
- **Size:** 7,043 rows x 21 columns, one row per customer.
- **Target:** `Churn` (Yes / No).

## Data Cleaning and Transformation (Power Query)

- Converted `TotalCharges` from text to decimal. Its 11 blank values belong to brand-new customers (tenure = 0) and were replaced with `0`.
- Standardized seven add-on columns (`MultipleLines`, `OnlineSecurity`, `OnlineBackup`, `DeviceProtection`, `TechSupport`, `StreamingTV`, `StreamingMovies`) by merging "No internet service" and "No phone service" into "No".
- Cleaned `PaymentMethod` labels (removed "(automatic)") for readable charts.
- Converted `SeniorCitizen` from 0/1 to `Senior Citizen` Yes/No.
- Confirmed there are no null values or duplicate customer IDs.
- Created derived columns:
  - `Churn Flag` (1/0)
  - `Tenure Group` (0-12, 13-24, 25-48, 49-72 months) with `Tenure Order` as its sort key
  - `Charge Band` (Low < 35, Medium 35-70, High > 70) with `Charge Order` as its sort key

## DAX Measures

```dax
Total Customers = DISTINCTCOUNT ( Telco[customerID] )
Churned Customers = CALCULATE ( [Total Customers], Telco[Churn] = "Yes" )
Retained Customers = [Total Customers] - [Churned Customers]
Churn Rate = DIVIDE ( [Churned Customers], [Total Customers] )
Overall Churn Rate = CALCULATE ( [Churn Rate], ALL ( Telco ) )
Churn vs Overall = [Churn Rate] - [Overall Churn Rate]
Monthly Revenue = SUM ( Telco[MonthlyCharges] )
Monthly Revenue at Risk = CALCULATE ( [Monthly Revenue], Telco[Churn] = "Yes" )
Revenue at Risk % = DIVIDE ( [Monthly Revenue at Risk], [Monthly Revenue] )
Avg Monthly Charge = AVERAGE ( Telco[MonthlyCharges] )
Avg Tenure (Months) = AVERAGE ( Telco[tenure] )
Avg Lifetime Value = AVERAGE ( Telco[TotalCharges] )
Churn Rate Color =
SWITCH (
    TRUE (),
    [Churn Rate] >= 0.35, "#D64545",
    [Churn Rate] >= 0.20, "#F2A93B",
    "#2E9E6B"
)
```

`Churn Rate Color` drives conditional formatting so bars turn red, amber or green by risk level.

## Business Recommendations

Based on the patterns above:

1. **Move month-to-month customers to longer contracts** with discounts or perks. This is the largest churn segment by far.
2. **Focus retention on the first 12 months**, for example with onboarding calls or early-tenure offers, since nearly half of new customers leave in that window.
3. **Investigate fiber optic service quality and pricing.** It has the highest churn of any internet type, which may point to price or reliability issues.
4. **Encourage automatic payments.** Electronic check users churn at nearly three times the rate of automatic-payment users.
5. **Promote tech support and online security add-ons**, since customers who have them churn about half as often. This is an association in the data, not proof of cause.

## Tools Used

- **Power BI Desktop:** data modeling, DAX, interactive visuals, custom dark theme
- **Power Query:** data cleaning and transformation
- **Custom JSON theme** and custom KPI icons for a consistent dark look

## Repository Structure

```
churnscope-telco-dashboard/
├── README.md
├── data/
│   └── WA_Fn-UseC_-Telco-Customer-Churn.csv
├── dashboard/
│   └── ChurnScope_Telco_Dashboard.pbix
├── theme/
│   └── telco_dark_theme_v2.json
├── icons/
    └── (KPI icons, PNG and SVG)
```

## How to Use

1. Download `ChurnScope_Telco_Dashboard.pbix` from the `dashboard/` folder.
2. Open it in **Power BI Desktop** (free download from Microsoft).
3. If the data source path shows an error, go to Home > Transform data > Data source settings and point it to `data/WA_Fn-UseC_-Telco-Customer-Churn.csv`.

## Limitations

- The dataset is a single snapshot, so it shows **who** churned but not **when**, which rules out trend or forecasting analysis.
- Findings describe associations, not causes.

## Author

**Sandeep Sanil**
BSc Computer Science | Aspiring Data Analyst
[LinkedIn](https://www.linkedin.com/in/your-profile) | [GitHub](https://github.com/your-username)

*Dataset credit: IBM sample data, Telco Customer Churn.*
