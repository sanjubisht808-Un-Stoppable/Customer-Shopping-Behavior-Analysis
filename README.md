# 🛍️ Customer Shopping Behavior Analysis

An end-to-end data analytics project that turns raw retail transaction data into business insights using **Python, PostgreSQL, and Power BI**.

![Python](https://img.shields.io/badge/Python-pandas-3776AB?logo=python&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/SQL-PostgreSQL-4169E1?logo=postgresql&logoColor=white)
![Power BI](https://img.shields.io/badge/Dashboard-Power%20BI-F2C811?logo=powerbi&logoColor=black)

---

## 📌 Business Problem

A retail company has noticed shifting purchase patterns across demographics, product categories, and sales channels. Management wants to know which factors (discounts, reviews, seasons, payment preferences, subscriptions) drive purchases and repeat buying.

> **Core question:** *How can the company leverage consumer shopping data to identify trends, improve customer engagement, and optimize marketing and product strategies?*

The full problem statement is in [`Business_Problem__Document.pdf`](docs/Business_Problem__Document.pdf).

---

## 🗂️ Dataset

| Property | Detail |
|---|---|
| Rows | 3,900 purchases |
| Columns | 18 |
| Missing values | 37 in `Review Rating` |
| File | `customer_shopping_behavior.csv` |

**Feature groups**

- **Demographics:** Customer ID, Age, Gender, Location, Subscription Status
- **Purchase details:** Item Purchased, Category, Purchase Amount (USD), Size, Color, Season
- **Behavior:** Discount Applied, Promo Code Used, Previous Purchases, Frequency of Purchases, Review Rating, Shipping Type, Payment Method

---

## 🔄 Workflow

```
Raw CSV  →  Python (clean + feature engineering)  →  PostgreSQL (10 business queries)  →  Power BI dashboard  →  Insights & recommendations
```

### 1. Data Preparation (Python)

Notebook: [`Customer_Shopping_Behavior_Analysis.ipynb`](notebooks/Customer_Shopping_Behavior_Analysis.ipynb)

- Loaded data with `pandas` and explored it with `df.info()` and `df.describe()`
- Imputed missing `review_rating` values using the **median rating of each product category**
- Standardized column names to **snake_case** (e.g. `Purchase Amount (USD)` → `purchase_amount`)
- Engineered features:
  - `age_group`: customers split into four quartile-based groups (Young Adult, Adult, Middle-aged, Senior)
  - `purchase_frequency_days`: purchase frequency text mapped to days (e.g. Weekly → 7, Quarterly → 90)
- Verified `discount_applied` and `promo_code_used` were identical, then dropped `promo_code_used`
- Loaded the cleaned DataFrame into PostgreSQL with `SQLAlchemy` (`customer` table)

### 2. Data Analysis (SQL)

Queries: [`customer_behavior_sql_queries.sql`](sql/customer_behavior_sql_queries.sql)

Techniques used: aggregations, subqueries, CTEs, `CASE` expressions, and window functions (`ROW_NUMBER() OVER (PARTITION BY ...)`).

| # | Business question |
|---|---|
| 1 | Total revenue by gender |
| 2 | Discount users who still spent above the average purchase amount |
| 3 | Top 5 products by average review rating |
| 4 | Average purchase amount: Standard vs. Express shipping |
| 5 | Subscribers vs. non-subscribers: average spend and total revenue |
| 6 | Top 5 products by share of purchases made with a discount |
| 7 | Customer segmentation into New, Returning, and Loyal |
| 8 | Top 3 most purchased products within each category |
| 9 | Subscription split among repeat buyers (more than 5 previous purchases) |
| 10 | Revenue contribution by age group |

### 3. Dashboard (Power BI)

File: [`customer_behavior_dashboard.pbix`](dashboard/customer_behavior_dashboard.pbix)

![Customer Behavior Dashboard](images/dashboard.png)

**KPIs:** 3.9K customers · $59.76 average purchase amount · 3.75 average review rating

**Visuals:** subscription split, revenue and sales by category, revenue and sales by age group

**Filters:** subscription status, gender, category, shipping type

---

## 📊 Key Findings

| Area | Result |
|---|---|
| **Total revenue** | $233,081 across 3,900 purchases |
| **Gender** | Male customers generated $157,890 vs. $75,191 for female customers. Men are also about 68% of the customer base (2,652 of 3,900) |
| **Subscriptions** | Only 27% of customers subscribe. Average spend is nearly identical: $59.49 (subscribers) vs. $59.87 (non-subscribers) |
| **Shipping** | Express orders average $60.48 vs. $58.46 for Standard |
| **Top-rated products** | Gloves (3.86), Sandals (3.84), Boots (3.82), Hat (3.80), Skirt (3.78) |
| **Most discount-reliant products** | Hat (50.00%), Sneakers (49.66%), Coat (49.07%), Sweater (48.17%), Pants (47.37%) |
| **Best sellers** | Accessories: Jewelry · Clothing: Blouse, Pants · Footwear: Sandals · Outerwear: Jacket |
| **Categories** | Clothing leads in revenue and sales volume, followed by Accessories, Footwear, and Outerwear |
| **Age groups** | Revenue is spread fairly evenly, with Young Adult highest ($62,143) and Senior lowest ($55,763) |
| **Loyalty segments** | 3,116 Loyal, 701 Returning, 83 New |

### ⚠️ Limitations worth knowing

- **Age groups are quartiles**, so each group holds roughly the same number of customers by design. Revenue differences between groups are therefore small and should not be over-interpreted.
- **Segment thresholds** (New = 1 previous purchase, Returning = 2 to 10, Loyal = 11+) put about 80% of customers in "Loyal". The thresholds may need tuning to separate customers more usefully.
- **Repeat buyers and subscriptions (Q9):** the raw counts (2,518 non-subscribers vs. 958 subscribers) mostly reflect that non-subscribers outnumber subscribers overall. Compare subscription *rates* between repeat and non-repeat buyers before drawing a conclusion.
- The dataset has no dates or cost data, so trend-over-time and margin analysis are not possible.

---

## 💡 Business Recommendations

1. **Boost subscriptions:** only 27% of customers subscribe and subscribers do not currently spend more, so add exclusive subscriber benefits to give the program a reason to exist.
2. **Customer loyalty programs:** reward repeat buyers and re-tune the segment thresholds so the program targets the right groups.
3. **Review discount policy:** several products are sold on discount about half the time. Balance sales lift against margin.
4. **Product positioning:** feature top-rated and best-selling products in campaigns.
5. **Targeted marketing:** focus on higher-revenue segments and Express-shipping customers, who spend slightly more per order.

---

## 🛠️ Tech Stack

- **Python:** pandas, SQLAlchemy, psycopg2
- **Database:** PostgreSQL (the notebook also includes MySQL and SQL Server connection snippets)
- **BI:** Power BI Desktop
- **Tools:** Jupyter Notebook, pgAdmin

---

## 📁 Repository Structure

```
├── data/
│   └── customer_shopping_behavior.csv
├── notebooks/
│   └── Customer_Shopping_Behavior_Analysis.ipynb
├── sql/
│   └── customer_behavior_sql_queries.sql
├── dashboard/
│   └── customer_behavior_dashboard.pbix
├── docs/
│   ├── Business_Problem__Document.pdf
│   └── Customer_Shopping_Behavior_Analysis.pdf   # full project report
├── images/
│   └── dashboard.png
└── README.md
```

---

## ▶️ How to Run

1. **Clone the repo**
   ```bash
   git clone https://github.com/<your-username>/<your-repo-name>.git
   cd <your-repo-name>
   ```
2. **Install dependencies**
   ```bash
   pip install pandas sqlalchemy psycopg2-binary jupyter
   ```
3. **Create a PostgreSQL database** named `customer_behavior`.
4. **Set your database credentials** as environment variables (never hardcode them):
   ```bash
   export DB_USER=postgres
   export DB_PASSWORD=your_password
   ```
   Then read them in the notebook with `os.environ["DB_PASSWORD"]`.
5. **Run the notebook** to clean the data and load it into the `customer` table.
6. **Run the SQL queries** in `sql/customer_behavior_sql_queries.sql` using pgAdmin or `psql`.
7. **Open the dashboard** in Power BI Desktop (`dashboard/customer_behavior_dashboard.pbix`).

---

## 👤 Author

**Sanjay Singh**
Aspiring Data Analyst


---

⭐ If you found this project useful, consider giving it a star!
