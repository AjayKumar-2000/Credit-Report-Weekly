# 💳 Credit Card & Customer Analytics — End-to-End BI Project

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)
![CSV](https://img.shields.io/badge/Data-CSV-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)

A fully integrated, end-to-end data analytics pipeline that transforms raw credit card transactional records and customer demographics into real-time business intelligence dashboards — covering $57M in revenue across 667K+ transactions.

---

## 🗂️ Project Structure

```
credit-card-analytics/
│
├── data/
│   ├── credit_card.csv          # Account snapshots (~10K rows tracking 667K+ aggregate transactions)
│   └── customer.csv             # Customer demographic records (~10K rows)
├── sql/
│   ├── schema.sql               # Database & table creation scripts
│   └── ingest.sql               # LOAD DATA INFILE ingestion commands
│
├── powerbi/
│   └── CreditCard_Analytics.pbix  # Power BI Desktop report file
│
├── assets/                      # Dashboard screenshot exports
│
└── README.md
```

---

## 🏗️ Architecture Overview

```
CSV Data Sources  ──►  MySQL (ccdb)  ──►  Power Query  ──►  Power BI Dashboards
(credit_card.csv        Schema +           Star Schema        KPIs, Charts,
 customer.csv)          Ingestion          + DAX Measures      Filters, Slicers
```

The pipeline follows a standard data engineering layout optimized for performance and scalability:

| Layer | Technology | Role |
|---|---|---|
| **Source** | CSV Files | Raw transaction & demographic data |
| **Storage** | MySQL (`ccdb`) | Relational DB with PK/FK constraints |
| **Transform** | Power Query | Multi-source blending, star-schema modeling |
| **Analytics** | DAX (Power BI) | KPI calculations, WoW growth metrics |
| **Visualization** | Power BI Desktop | Interactive dashboards with slicers |

---

## 🗄️ Database Schema

The `ccdb` database uses a **1-to-Many relational join** via `Client_Num`.

### `cc_detail` — Transaction Table
| Column | Type | Description |
|---|---|---|
| `Client_Num` | INT | Foreign key → `cust_detail` |
| `Card_Category` | VARCHAR(20) | Blue / Silver / Gold / Platinum |
| `Annual_Fees` | INT | Annual card fee |
| `Week_Start_Date` | DATE | Week-level date (parsed via `STR_TO_DATE`) |
| `Qtr` | VARCHAR(10) | Quarter label (Q1–Q4) |
| `Credit_Limit` | DECIMAL(10,2) | Assigned credit limit |
| `Total_Trans_Amt` | INT | Weekly transaction amount |
| `Total_Trans_Ct` | INT | Weekly transaction count |
| `Interest_Earned` | DECIMAL(10,3) | Interest revenue |
| `Avg_Utilization_Ratio` | DECIMAL(10,3) | Credit utilization |
| `Use_Chip` | VARCHAR(10) | Swipe / Chip / Online |
| `Exp_Type` | VARCHAR(50) | Bills / Fuel / Travel / etc. |
| `Delinquent_Acc` | VARCHAR(5) | Delinquency flag |

### `cust_detail` — Customer Demographics Table
| Column | Type | Description |
|---|---|---|
| `Client_Num` | INT | Primary key |
| `Customer_Age` | INT | Customer age |
| `Gender` | VARCHAR(5) | M / F |
| `Education_Level` | VARCHAR(50) | Graduate / High School / etc. |
| `Marital_Status` | VARCHAR(20) | Married / Single / Unknown |
| `State_cd` | VARCHAR(50) | US state code |
| `Customer_Job` | VARCHAR(50) | Businessman / White-collar / etc. |
| `Income` | INT | Annual income |
| `Cust_Satisfaction_Score` | INT | CSS rating |

---

## ⚡ Setup & Replication Guide

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/credit-card-analytics.git
cd credit-card-analytics
```

### 2. Set Up MySQL Database
```sql
-- Run schema creation
SOURCE sql/schema.sql;
```

### 3. Ingest CSV Data
```sql
-- Load transaction data
LOAD DATA LOCAL INFILE 'data/credit_card.csv'
INTO TABLE cc_detail
FIELDS TERMINATED BY ','
IGNORE 1 ROWS
(@Client_Num, ..., @Week_Start_Date, ...)
SET Week_Start_Date = STR_TO_DATE(@Week_Start_Date, '%d-%m-%Y');

-- Load customer data
LOAD DATA LOCAL INFILE 'data/customer.csv'
INTO TABLE cust_detail
FIELDS TERMINATED BY ','
IGNORE 1 ROWS;
```

### 4. Open in Power BI Desktop
- Open `powerbi/CreditCard_Analytics.pbix`
- Update the MySQL connection string to point to your local server
- Refresh the dataset

---

## 📈 Key Metrics & Findings (YTD)

| KPI | Value |
|---|---|
| 💰 Total Revenue | **$57M** |
| 💳 Transaction Amount | **$45.5M** |
| 📈 Interest Earned | **$7.98M** |
| 🔢 Transaction Count | **667.2K** |
| ⚡ WoW Revenue Growth | **+28.8%** |
| ✅ Activation Rate | **57.5%** |
| ⚠️ Delinquency Rate | **6.06%** |

### Dashboard 1 — Credit Card Transaction Report

- **Blue card** dominates with **$47M** revenue (83% of total portfolio)
- **Swipe** is the leading payment channel at **$36M** vs Online at just $4M
- **Bills** is the top expense category at **$14M**
- Graduates generate the highest revenue by education at **$23M**
- Businessmen lead by job type at **$18M**

### Dashboard 2 — Credit Card Customer Report

- **Male** customers contribute **$31M** vs Female at **$26M**
- The **40–50 age bracket** is the top revenue segment at **$25M**
- **TX, NY, CA** together generate **68%** of total revenue
- **Married** customers outspend single customers across all channels
- **High income group** drives **$23M** in revenue

---

## 🎯 Strategic Recommendations

1. **🌐 Boost Digital Channel Adoption** — Online transactions account for only $4M vs $36M Swipe; launch virtual card and e-commerce rewards programs.
2. **💎 Premium Tier Upsell Campaigns** — 93% of volume sits in Blue/Silver; identify high-income customers for Gold/Platinum migration.
3. **📍 Geo-Targeted Marketing** — Concentrate marketing budgets in TX, NY, and CA which drive 68% of revenue.
4. **🚨 Delinquency Early-Warning Models** — With a 6.06% delinquency rate, deploy predictive utilization-spike alerts for proactive risk management.

---

## 🛠️ Tech Stack

- **Database:** MySQL
- **BI Tool:** Power BI Desktop
- **Data Modeling:** Star Schema + DAX
- **ETL:** SQL `LOAD DATA LOCAL INFILE` + Power Query
- **Source Data:** CSV flat files

---

> Built as a full end-to-end data engineering + BI project — from raw CSV ingestion to interactive executive dashboards.
