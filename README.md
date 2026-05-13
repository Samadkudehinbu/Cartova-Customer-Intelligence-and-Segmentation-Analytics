# Cartova Customer Intelligence & Segmentation Analytics
> A customer intelligence project analyzing 11,332 Cartova retail customers across demographics, purchase behavior, loyalty tiers, and satisfaction — built as a three-page interactive Power BI dashboard for the May 2026 PowerBI Initiative Monthly Challenge.

---
🔗 **[View Live Report](https://app.powerbi.com/view?r=eyJrIjoiZGI3ZWM3ZmItNjFkMS00MWIyLTlmMWUtMGVjOTk2MjY1Mjg0IiwidCI6IjgxMTQ1ZWNkLTc5NTAtNDk4Ny1hOGFmLTJhMDY1YTgwMWVhYyJ9)** &nbsp;|&nbsp; 📃
**[Dataset](Dataset.xlsx)** &nbsp;|&nbsp; 📖
**[Medium Article](https://medium.com/@kudehinbusamad/cartova-customer-intelligence-segmentation-analytics-a-power-bi-case-study-135a2461e4af)**
---
## 🧭 Project Overview

Cartova is a growing retail brand with a customer base spanning multiple generations, income levels, and geographic regions. Leadership needed answers to a core set of business questions: who are their most valuable customers, what drives loyalty, where is satisfaction slipping, and which customers are at risk of leaving?

This project transforms 11,332 raw customer records into a three-page Power BI intelligence report that profiles customers, segments them by risk and value, and delivers channel-specific retention recommendations.

---

## 🏢 Business Context

**Retention Problem**
Without a structured risk model, Cartova was relying on reactive outreach to retain customers. The analysis introduces a quantified churn risk score built from recency, loyalty, and satisfaction signals — giving the business a proactive retention lever.

**Segment Blind Spots**
Different age groups, income bands, and locations generate different levels of value. Marketing spend was not differentiated across these groups. The dashboard creates clear demographic and behavioural profiles to support more targeted allocation.

**Channel Inefficiency**
Cartova communicates across four channels — email, SMS, phone, and in-person — without a data-backed view of which channel works for which customer segment. The analysis identifies the right channel for high-value versus at-risk customers.

---

## 🛠️ Tools & Tech Stack

| Tool | Purpose |
|------|---------|
| **Power Query** | Data Transformation|
| **Power BI Desktop** | Data modelling, DAX measures, interactive dashboard development |
| **Figma** | Wireframe design and custom background layout for all three report pages |

---

## 📦 Dataset

**Source:** PowerBI Initiative — May Challenge Dataset  
**Records:** 11,332 unique customers (filtered from 12,145 raw records after cleaning)  
**Time Coverage:** Membership start dates from 2014 through 2023; last purchase dates through August 2024

You can download the dataset [here](Dataset.xlsx).

### Fields

| Column | Type | Description |
|--------|------|-------------|
| Customer ID | Text | Primary key |
| Customer Name | Text | Full name |
| Customer Age | Integer | Age in years |
| Gender | Text | Male / Female / Unspecified |
| Location | Text | City-level location |
| Annual Income ($) | Decimal | Household annual income |
| Purchase History | Integer | Total number of purchases |
| Loyalty Points | Integer | Accumulated loyalty points |
| Last Purchase Date | Date | Date of most recent transaction |
| Customer Feedback | Text | Satisfaction rating (5 levels) |
| Preferred Contact Method | Text | Email / SMS / Phone / In-Person |
| Marital Status | Text | Single / Married / Divorced / Widowed |
| Number of Dependents | Integer | Count of dependents in household |
| Membership Start Date | Date | Date customer joined |

### Calculated Columns (Derived Using DAX)

| Column | Logic | Dax Formula |
|--------|-------|-------------|
| Age Group | Age mapped to generational label with explicit range | `SWITCH(TRUE(), 'Customer Data'[Customer Age] <= 27, "Gen Z (<28)", 'Customer Data'[Customer Age] <= 43, "Millennials (28-43)", 'Customer Data'[Customer Age] <= 59, "Gen X (44-59)", "Baby Boomers (60+)")` |
| Age Group Sort | Integer for correct chronological chart ordering | `SWITCH(TRUE(), 'Customer Data'[Customer Age] <= 27, 1, 'Customer Data'[Customer Age] <= 43, 2, 'Customer Data'[Customer Age] <= 59, 3, 4)` |
| Income Band | Low (<$40k) / Medium ($40k–$80k) / High ($80k–$120k) / Very High (>$120k) | `SWITCH(TRUE(), 'Customer Data'[Annual Income] < 40000, "Low (<$40k)", 'Customer Data'[Annual Income] < 80000, "Medium ($40k-$80k)", 'Customer Data'[Annual Income] < 120000, "High ($80k-$120k)", "Very High (>$120k)")` |
| Income Band Sort | Integer for correct income ordering | `SWITCH(TRUE(), 'Customer Data'[Annual Income] < 40000, 1, 'Customer Data'[Annual Income] < 80000, 2, 'Customer Data'[Annual Income] < 120000, 3, 4)` |
| Tenure Days | Day difference between Membership Start Date and Last Purchase Date | `DATEDIFF('Customer Data'[Membership Start Date], 'Customer Data'[Last Purchase Date], DAY)` |
| Tenure Band | <1 Year / 1–3 Years / 3+ Years | `VAR TenureDays = DATEDIFF('Customer Data'[Membership Start Date], 'Customer Data'[Last Purchase Date], DAY) RETURN SWITCH(TRUE(), TenureDays < 365, "<1 Year", TenureDays < 1095, "1-3 Years", "3+ Years")` |
| Tenure Band Sort | Integer for correct tenure ordering | `VAR TenureDays = DATEDIFF('Customer Data'[Membership Start Date], 'Customer Data'[Last Purchase Date], DAY) RETURN SWITCH(TRUE(), TenureDays < 365, 1, TenureDays < 1095, 2, 3)` |
| Loyalty Tier | Bronze (<1k) / Silver (1k-5k) / Gold (5k-10k) / Platinum (10k+) based on loyalty point thresholds | `SWITCH(TRUE(), 'Customer Data'[Loyalty Points] >= 10000, "Platinum (10k+)", 'Customer Data'[Loyalty Points] >= 5000, "Gold (5k-10k)", 'Customer Data'[Loyalty Points] >= 1000, "Silver (1k-5k)", "Bronze (<1k)")` |
| Loyalty Tier Sort | Integer for correct loyalty ordering | `SWITCH(TRUE(), 'Customer Data'[Loyalty Points] >= 10000, 4, 'Customer Data'[Loyalty Points] >= 5000, 3, 'Customer Data'[Loyalty Points] >= 1000, 2, 1)` |
| Churn Risk Score | Weighted composite: Recency (50%) + Loyalty (30%) + Feedback (20%) | `VAR DaysSinceLastPurchase = DATEDIFF('Customer Data'[Last Purchase Date], TODAY(), DAY) VAR LoyaltyScore = SWITCH(TRUE(), 'Customer Data'[Loyalty Points] > 10000, 1, 'Customer Data'[Loyalty Points] > 5000, 2, 'Customer Data'[Loyalty Points] > 1000, 3, 4) VAR RecencyScore = SWITCH(TRUE(), DaysSinceLastPurchase <= 30, 1, DaysSinceLastPurchase <= 90, 2, DaysSinceLastPurchase <= 180, 3, 4) VAR FeedbackScore = SWITCH('Customer Data'[Customer Feedback], "Very Satisfied", 1, "Satisfied", 2, "Neutral", 3, "Dissatisfied", 4, "Very Dissatisfied", 5, 3) RETURN (RecencyScore * 0.5) + (LoyaltyScore * 0.3) + (FeedbackScore * 0.2)` |
| Churn Risk Category | High / Medium / Low derived from Churn Risk Score using 25th and 75th percentiles | `VAR Score = [Churn Risk Score] VAR P25 = PERCENTILE.INC('Customer Data'[Churn Risk Score], 0.25) VAR P75 = PERCENTILE.INC('Customer Data'[Churn Risk Score], 0.75) RETURN SWITCH(TRUE(), Score <= P25, "Low Risk", Score <= P75, "Medium Risk", "High Risk")` |

---

## 🧹 Data Preparation

### Missing Values
- **Customer Feedback (47 records):** Blank entries were removed to preserve row count without skewing satisfaction metrics.
- **Gender (89 records):** Inconsistent/mismatched entries flagged were excluded from gender-based segmentation visuals.

### Income Outliers
Income ranged from $20,000 to nearly $300,000. Rather than capping outliers, an Income Band column was created to enable band-level segmentation without distortion from extreme values.

### Generation Mapping
Ages were mapped to generational labels with explicit ranges for business readability. A sort-order column was added to enforce correct visual ordering (Gen Z → Millennials → Gen X → Baby Boomers) across all charts.

### Date Model
Two active date tables were created — one connected to `Last Purchase Date` and one to `Membership Start Date` — enabling independent time intelligence for activity analysis and acquisition cohort analysis.

### Cleaning Summary

| Step | Records Affected | Outcome |
|------|-----------------|---------|
| Missing feedback | 47 | Removed |
| Gender inconsistencies | 89 | Removed |
| Generation labeling | 11,332 | Complete |
| Income band creation | 11,332 | Complete |
| Churn risk scoring | 11,332 | Complete |

---

## 📊 Dashboard Pages

### Page 1 — Customer Profile & Segmentation
Demographic overview of Cartova's customer base. KPI cards surface total customers, average income, average loyalty points, satisfaction rate, and total purchases — all filterable by year, month, marital status, and number of dependents. Charts profile customers by age group, average income by generation, satisfaction rate by generation, purchase volume by generation, purchase behaviour by marital status, and purchases by number of dependents.

<img width="1603" height="872" alt="Page 1" src="https://github.com/user-attachments/assets/f0a8fab7-8ee9-499a-926f-0244fe8befcd" />

### Page 2 — Satisfaction, Churn & Channel Performance
Risk and engagement intelligence. KPI cards track total customers, high-risk customer count, average churn risk score, satisfaction rate, and dissatisfaction rate. A stacked bar chart breaks down customer feedback sentiment across all four age groups. Segmented views show preferred contact method behaviour separately for high-value customers and high-risk customers. A location intelligence table surfaces city-level churn scores and risk categories.

<img width="1607" height="872" alt="Page 2" src="https://github.com/user-attachments/assets/79018193-a376-4478-b51b-7de568d2b40d" />


### Page 3 — Customer Intelligence Table
Drill-through customer-level detail table. Filterable by loyalty tier, churn category, customer feedback, and location. Displays all key attributes per customer — income band, loyalty points, loyalty tier, churn score, churn risk category, and preferred contact method — formatted with conditional colour-coded badges for quick scanning.

<img width="1606" height="875" alt="Page 3" src="https://github.com/user-attachments/assets/0c1fce5a-ce5a-4695-821c-a317ac88a2bd" />

---

## 🧮 DAX Measures

| Measure | Description | Dax Formula |
|---------|-------------|-------------|
| Total Customers | Count of unique Customer IDs | `DISTINCTCOUNT('Customer Data'[Customer ID])` |
| Total Purchases | Sum of Purchase History | `SUM('Customer Data'[Purchase History])` |
| Average Income | Average of Annual Income | `AVERAGE('Customer Data'[Annual Income])` |
| Average Loyalty Points | Average of Loyalty Points | `AVERAGE('Customer Data'[Loyalty Points])` |
| Satisfaction Rate | % of customers rated Satisfied or Very Satisfied | `VAR SatisfiedCount = CALCULATE([Total Customers], 'Customer Data'[Customer Feedback] IN {"Very Satisfied", "Satisfied"}) RETURN DIVIDE(SatisfiedCount, [Total Customers], 0)` |
| Dissatisfaction Rate | % of customers rated Dissatisfied or Very Dissatisfied | `VAR DissatisfiedCount = CALCULATE([Total Customers], 'Customer Data'[Customer Feedback] IN {"Dissatisfied", "Very Dissatisfied"}) RETURN DIVIDE(DissatisfiedCount, [Total Customers], 0)` |
| High Risk Customers | Count of customers in High Churn Risk Category | `CALCULATE([Total Customers], 'Customer Data'[Churn Risk Category] = "High Risk")` |
| Average Churn Risk Score | Average of Churn Risk Score | `AVERAGE('Customer Data'[Churn Risk Score])` |
| Previous Year High Risk Customers | YoY comparison of High Risk Customers using DATEADD | `VAR CurrentYear = [High Risk Customers] VAR LastYear = CALCULATE([High Risk Customers], DATEADD('Membership Date'[Date], -1, YEAR)) RETURN CurrentYear - LastYear` |
| Previous Year Total Purchases | YoY comparison of Total Purchases using DATEADD | `VAR CurrentYear = [Total Purchases] VAR LastYear = CALCULATE([Total Purchases], DATEADD('Purchase Date'[Date], -1, YEAR)) RETURN CurrentYear - LastYear` |

---

## 💡 Key Insights

### Generational Value
Gen X drives the highest total purchase volume (142,847) and highest average loyalty points per customer (5,892). Millennials are close in purchase volume but accumulate fewer loyalty points. Gen Z, while the smallest segment, represents the highest growth upside — currently concentrated in Bronze loyalty tier with limited engagement depth.

### Income & Satisfaction Correlation
Higher income directly correlates with higher satisfaction and lower churn risk. Very High income customers report a 78% satisfaction rate and an average churn risk score of 3.12. Low income customers report 61% satisfaction and a 3.62 average churn risk score — a 17-percentage-point satisfaction gap that tracks cleanly with risk.

### First-Year Churn is the Biggest Problem
42% of all customers go quiet within their first year of membership. The drop-off is steep: retention improves significantly after month 12, and customers who reach year two are far more likely to reach year three. Year one is Cartova's single largest retention opportunity.

### Geographic Dissatisfaction Clusters
Five locations — Port Lisa, Lake Jason, West Kimberly, North Stephaniehaven, and New Michael — account for nearly 200 dissatisfied customers, with local dissatisfaction rates of 13–18% versus a company average of 6.2%. The clustering pattern suggests operational or service issues specific to these areas rather than product-wide problems.

### Channel Split: Value vs Risk
High-value customers (income >$100k) prefer in-person (34%) and email (28%). At-risk customers show a completely different pattern — 38% prefer SMS, more than double the rate of high-value customers. This is either a channel preference or a signal that email and in-person engagement have already failed for this group.

### Family Structure & Spend
Married customers outspend single customers by 55% on average (34.2 vs 22.1 purchases). Customers with 3+ dependents outspend those with zero dependents by nearly 2x. Household size is a strong predictor of purchase volume.

### Loyalty Tier Concentration
Gen X holds the highest proportion of Platinum and Gold members (18% and 32%). 50% of Gen Z customers are in Bronze. Loyalty tier tracks strongly with both age and income — lower-income and younger customers are consistently underrepresented at the top of the tier structure.

### Churn Risk Model Performance
The three-tier risk model segments customers cleanly by behavior. High-risk customers last purchased an average of 187 days ago and hold 83% fewer loyalty points than low-risk customers. Medium-risk customers — the largest group at 49% — are still active enough (94-day average recency) to recover with targeted intervention.

---

## ✅ Recommendations

### Retention
- Launch a 90-day re-engagement flow for Medium Risk customers using purchase history-based personalization. This 49% segment is still reachable.
- Implement win-back campaigns for High Risk customers within 30 days of crossing the 150-day inactivity threshold — recovery rates fall sharply after 180 days.
- Introduce a 2x points multiplier for the first 90 days of membership to reduce first-year churn (currently 42%).

### Segment Targeting
- Direct premium product and loyalty upgrade offers to Gen X and Millennials — these two generations drive 70% of total purchases.
- Build an entry-level engagement path for Gen Z focused on frequency over spend: small, frequent rewards for reviews, check-ins, or referrals.
- Apply income-based messaging: emphasize value and savings for Low/Medium income bands; emphasize exclusivity and priority access for High/Very High bands.

### Geographic Action
- Deploy localized satisfaction surveys and service audits in Port Lisa, Lake Jason, and West Kimberly first — they account for 127 of the 200 dissatisfied customers in the five flagged locations.
- Run location-specific reactivation offers for customers in high-dissatisfaction areas who have not purchased in 90+ days.

### Channel Optimization
- Shift high-value customer outreach to email and in-person — these two channels capture 62% of high-value customer preference.
- Move at-risk retention campaigns to SMS — 38% of at-risk customers prefer this channel, double the rate seen in high-value segments.
- Reduce phone outreach for both groups — it's mid-ranked for both and unlikely to be the most efficient channel for volume campaigns.

### Loyalty Strategy
- Launch a 60-day double-points promotion for Bronze and Silver members to accelerate Gen Z and low-income customers into higher tiers.
- Introduce household account linking — married customers with dependents spend 55% more than singles, and a family loyalty program could consolidate that spend and extend retention.

---

## 🔗 Connect Links

- **Portfolio:** [abdulsamadportfolio](https://sites.google.com/view/abdulsamadportfolio/home)
- **GitHub:** [Samadkudehinbu](https://github.com/Samadkudehinbu)
- **LinkedIn:** [abdulsamad-kudehinbu](https://linkedin.com/in/abdulsamad-kudehinbu/)

---

*Built by Abdulsamad Kudehinbu — Power BI Developer & Data Analyst*  
*May 2026 | PowerBI Initiative Monthly Challenge*
