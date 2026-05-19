<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:1a0533,50:3d0066,100:7209b7&height=210&section=header&text=Store%20Sales%20Analysis&fontSize=46&fontColor=f8f0ff&fontAlignY=38&desc=Sales%20Performance%20%26%20Profitability%20%7C%20SQL%20%7C%2025%20Queries&descAlignY=58&descSize=15&descColor=d0a8ff&animation=fadeIn" width="100%"/>

<p>
  <img src="https://img.shields.io/badge/MySQL-8.0-4479A1?style=for-the-badge&logo=mysql&logoColor=white"/>
  <img src="https://img.shields.io/badge/SQL%20Queries-25%20Total-7209b7?style=for-the-badge&logo=databricks&logoColor=white"/>
  <img src="https://img.shields.io/badge/Records-9%2C993%20Orders-3d0066?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Period-2019–2022-f72585?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Revenue-$2.3M-b5179e?style=for-the-badge"/>
</p>

<p>
  <a href="#-business-problem">Problem</a> •
  <a href="#-dataset">Dataset</a> •
  <a href="#-sql-queries">SQL Queries</a> •
  <a href="#-key-insights">Insights</a> •
  <a href="#-project-structure">Structure</a> •
  <a href="#-getting-started">Setup</a>
</p>

</div>

---

## 🧩 Business Problem

> A mid-sized US retail company generates **$2.3M in sales** across 4 regions — but sits at only a **12.47% profit margin**.

This project uses structured SQL analysis to pinpoint exactly where profit is leaking, which customers and categories are worth the investment, and what the data says about growth trajectory.

```
$2.3M Revenue  →  12.47% Margin  →  WHERE is the other 87.53% going?
```

| # | Problem Statement | Focus Area |
|---|-------------------|-----------|
| P1 | Profit leakage from excessive discounting | Discount Analysis |
| P2 | Regional sales & profit imbalance | Regional Analysis |
| P3 | Customer segment profitability gap | Segment Analysis |
| P4 | Product category performance variance | Product Analysis |
| P5 | Shipping mode cost vs. revenue trade-off | Logistics Analysis |
| P6 | Year-over-year growth & seasonality patterns | Trend Analysis |

---

## 📁 Dataset

| Property | Detail |
|----------|--------|
| 📦 Records | 9,993 order lines |
| 📅 Period | 2019 – 2022 |
| 🌎 Country | United States |
| 🏷️ Categories | Furniture · Office Supplies · Technology |
| 👥 Segments | Consumer · Corporate · Home Office |
| 🗺️ Regions | West · East · Central · South |

### Schema — `store_sales` Table

```sql
CREATE TABLE store_sales (
    Order_ID       VARCHAR(255),
    Order_Date     DATE,
    Ship_Date      DATE,
    Ship_Mode      VARCHAR(255),   -- Standard / Second / First / Same Day
    Customer_ID    VARCHAR(255),
    Customer_Name  VARCHAR(255),
    Segment        VARCHAR(255),   -- Consumer / Corporate / Home Office
    Country        VARCHAR(255),
    City           VARCHAR(255),
    State          VARCHAR(255),
    Postal_Code    VARCHAR(255),
    Region         VARCHAR(255),   -- West / East / Central / South
    Product_ID     VARCHAR(255),
    Category       VARCHAR(255),   -- Furniture / Office Supplies / Technology
    Sub_Category   VARCHAR(255),
    Product_Name   VARCHAR(500),
    Sales          DECIMAL(10,4),
    Quantity       INT,
    Discount       DECIMAL(4,2),
    Profit         DECIMAL(10,4)
);
```

---

## 🗂️ SQL Queries

> **25 queries across 3 difficulty levels** — structured as a progressive deep-dive from baseline metrics to window function-powered analytics.

<details>
<summary><b>🟢 Basic Level · Q1–Q8 · Baseline Metrics</b></summary>

| # | Question | SQL Concepts |
|---|----------|-------------|
| Q1 | Total sales revenue and profit for the entire dataset | `SUM`, Aggregation |
| Q2 | Sales, profit, and orders by product Category | `GROUP BY`, `ROUND`, Profit Margin % |
| Q3 | Top 10 products by total sales revenue | `ORDER BY`, `LIMIT` |
| Q4 | Unique customers, orders, and products in dataset | `COUNT DISTINCT`, Cardinality |
| Q5 | Total sales and profit by Region | Multi-metric `GROUP BY` |
| Q6 | Total sales and profit by Customer Segment | Segment-level P&L |
| Q7 | Orders placed each year (2019–2022) | `YEAR()`, YoY volume trend |
| Q8 | Distribution of orders across Ship Modes | Order share % calculation |

**Sample Query — Q2: Category Performance**
```sql
SELECT
    Category,
    COUNT(DISTINCT Order_ID)          AS Total_Orders,
    SUM(Sales)                        AS Total_Sales,
    SUM(Profit)                       AS Total_Profit,
    ROUND(SUM(Profit)/SUM(Sales)*100, 2) AS Profit_Margin_Pct
FROM store_sales
GROUP BY Category
ORDER BY Total_Sales DESC;
```

</details>

<details>
<summary><b>🟠 Intermediate Level · Q9–Q17 · Diagnostic Queries</b></summary>

| # | Question | SQL Concepts |
|---|----------|-------------|
| Q9 | Monthly sales trend by year | `MONTH()`, `YEAR()`, time series |
| Q10 | Loss-making Sub-Categories | `HAVING SUM(Profit) < 0` |
| Q11 | Profit margin by discount bracket | `CASE WHEN` bucketing |
| Q12 | Top 10 customers by profit | Customer-level aggregation |
| Q13 | Average order value by Segment | `AVG`, segment comparison |
| Q14 | High sales but low profit States | Multi-condition filtering |
| Q15 | Year-over-Year growth by Category | Self-join or LAG pattern |
| Q16 | Average shipping time by Ship Mode | `DATEDIFF`, logistics SLA |
| Q17 | Products sold at a loss most frequently | Frequency + profitability join |

**Sample Query — Q11: Discount Bracket Analysis**
```sql
SELECT
    CASE
        WHEN Discount = 0          THEN 'No Discount'
        WHEN Discount <= 0.10      THEN '1–10%'
        WHEN Discount <= 0.20      THEN '11–20%'
        WHEN Discount <= 0.30      THEN '21–30%'
        WHEN Discount <= 0.40      THEN '31–40%'
        ELSE                            '>40%'
    END                                   AS Discount_Bracket,
    COUNT(*)                              AS Total_Orders,
    ROUND(SUM(Sales), 2)                  AS Total_Sales,
    ROUND(SUM(Profit), 2)                 AS Total_Profit,
    ROUND(SUM(Profit)/SUM(Sales)*100, 2)  AS Profit_Margin_Pct
FROM store_sales
GROUP BY Discount_Bracket
ORDER BY Profit_Margin_Pct DESC;
```

</details>

<details>
<summary><b>🔵 Advanced Level · Q18–Q25 · Window Functions & CTEs</b></summary>

| # | Question | SQL Concepts |
|---|----------|-------------|
| Q18 | Customer ranking by profit within Segment | `RANK() OVER (PARTITION BY Segment)` |
| Q19 | Cumulative sales by month (Running Total) | `SUM() OVER (ORDER BY month)` |
| Q20 | Top Sub-Category per Region | `ROW_NUMBER() OVER (PARTITION BY Region)` |
| Q21 | Category profit share of total | CTE + percentage calculation |
| Q22 | Customers active all 4 years (loyal buyers) | `HAVING COUNT(DISTINCT YEAR) = 4` |
| Q23 | Discount >40% with positive profit | Exception filtering — anomaly detection |
| Q24 | Sub-category margin health scorecard | `CASE WHEN` multi-tier classification |
| Q25 | Top 3 products per Sub-Category | `DENSE_RANK() OVER (PARTITION BY Sub_Category)` |

**Sample Query — Q18: Customer Ranking Within Segment**
```sql
SELECT
    Customer_Name,
    Segment,
    ROUND(SUM(Profit), 2) AS Total_Profit,
    RANK() OVER (
        PARTITION BY Segment
        ORDER BY SUM(Profit) DESC
    ) AS Profit_Rank_In_Segment
FROM store_sales
GROUP BY Customer_Name, Segment
ORDER BY Segment, Profit_Rank_In_Segment;
```

</details>

---

## 💡 Key Insights

| Area | Finding |
|------|---------|
| 🔴 Discounting | Orders with **>40% discount** result in a **100% loss rate** — zero exceptions across all 9,993 records |
| 📦 Sub-Category | **Tables** and **Bookcases** are confirmed loss-makers — negative profit despite positive sales volume |
| 🗺️ Region | **West** drives the highest revenue; **South** has the weakest profit margins |
| 👤 Customers | Top **20% of customers** generate ~**80% of total profit** (Pareto holds) |
| 📅 Seasonality | **Q4 (Oct–Dec)** is peak season; **Q1** is the slowest quarter consistently across all years |
| 🚚 Shipping | **Standard Class** dominates volume; **Same Day** has the lowest profit margin per order |
| 📈 Growth | Steady YoY revenue growth from 2019–2022 despite margin compression |

---

## 📁 Project Structure

```
store-sales-analysis-sql/
│
├── 📄 store_sales.csv                    # Raw dataset — 9,993 order lines
│
├── 🟢 01_basic_queries.sql               # Q1–Q8  · Baseline financial metrics
├── 🟠 02_intermediate_queries.sql        # Q9–Q17 · Diagnostic & trend analysis
├── 🔵 03_advanced_queries.sql            # Q18–Q25 · Window functions & CTEs
│
├── 🎯 Business Action.sql                # Actionable SQL recommendations
├── 💡 Expected Insights of Business.sql  # Pre-defined insight queries
│
├── 📊 Sales_Business_Analysis.pdf        # Full business analysis report
└── 📖 README.md                          # You are here
```

---

## 🚀 Getting Started

### Prerequisites
```
MySQL 8.0+   |   MySQL Workbench (recommended)
```

### 1. Clone the repository
```bash
git clone https://github.com/chayanshh/store-sales-analysis-sql.git
cd store-sales-analysis-sql
```

### 2. Create the database & table
Run the setup block at the top of `01_basic_queries.sql`:
```sql
CREATE DATABASE store_sales;
USE store_sales;

CREATE TABLE store_sales ( ... );  -- full schema in 01_basic_queries.sql
```

### 3. Load the dataset
```sql
SET GLOBAL local_infile = ON;

LOAD DATA LOCAL INFILE '/your/path/store_sales.csv'
INTO TABLE store_sales
FIELDS TERMINATED BY ','
OPTIONALLY ENCLOSED BY '"'
LINES TERMINATED BY '\r\n'
IGNORE 1 ROWS;
```
> Update the file path to match your local directory.

### 4. Run the queries

Execute in order for a progressive analysis:

```bash
01_basic_queries.sql          # Start here — baseline metrics
02_intermediate_queries.sql   # Diagnostic & trend analysis
03_advanced_queries.sql       # Advanced analytics with window functions
Business Action.sql           # Business recommendations
```

---

## 🧩 Analysis Pipeline

```mermaid
flowchart LR
    A[📄 store_sales.csv] --> B[🗃️ MySQL Table]
    B --> C[🟢 Basic Queries\nQ1–Q8]
    C --> D[🟠 Intermediate\nQ9–Q17]
    D --> E[🔵 Advanced\nQ18–Q25]
    E --> F[💡 Business Actions]
    F --> G[📊 PDF Report]

    style A fill:#1a0533,color:#f8f0ff
    style G fill:#7209b7,color:#f8f0ff
```

---

## 🛠️ SQL Concepts Covered

```
✅ Aggregations          SUM, COUNT, AVG, ROUND
✅ Filtering             WHERE, HAVING, CASE WHEN
✅ Grouping              GROUP BY multi-column, ORDER BY
✅ Date Functions        YEAR(), MONTH(), DATEDIFF()
✅ Subqueries            Correlated & uncorrelated
✅ CTEs                  WITH clause for readable logic
✅ Window Functions      RANK, ROW_NUMBER, DENSE_RANK, SUM OVER, PARTITION BY
✅ Data Loading          LOAD DATA LOCAL INFILE, SET global local_infile
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/Q26-cohort-analysis`
3. Add your query with a comment header explaining the business question
4. Open a Pull Request

---

## 👤 Author

**Chayansh Jain**
- GitHub: [@chayanshh](https://github.com/chayanshh)
- LinkedIn: [linkedin.com/in/chayanshh05](https://www.linkedin.com/in/chayanshh05)

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:7209b7,50:3d0066,100:1a0533&height=120&section=footer" width="100%"/>

*Built with 🟣 MySQL · Window Functions · CTEs · Business Intelligence*

</div>
