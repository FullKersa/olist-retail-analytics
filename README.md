# Olist Retail Analytics Pipeline

End-to-end retail analytics pipeline built on the **Brazilian E-Commerce Public Dataset by Olist** — covering star schema modeling, Python ETL, analytical SQL, and BI dashboard.

---

## Architecture Overview

```
data/raw/ (CSV)
     │
     ▼
pipeline/etl.py (Python + SQLAlchemy)
     │
     ▼
PostgreSQL — Star Schema
(fact_orders + dimension tables)
     │
     ▼
analysis/*.sql (Window Functions, CTEs, Cohorts)
     │
     ▼
Dashboard (Tableau / Apache Superset)
```

---

## Dataset

**Source:** [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) — Kaggle

| Table | Description |
|---|---|
| `olist_orders_dataset` | Order lifecycle and timestamps |
| `olist_order_items_dataset` | Items per order with price and freight |
| `olist_order_payments_dataset` | Payment method and installments |
| `olist_order_reviews_dataset` | Customer review scores and comments |
| `olist_customers_dataset` | Customer location and unique IDs |
| `olist_sellers_dataset` | Seller location |
| `olist_products_dataset` | Product category and dimensions |
| `olist_category_name_translation` | Category name in English |
| `olist_geolocation_dataset` | ZIP code to lat/lng mapping |

> Download the dataset from Kaggle and place all CSV files inside `data/raw/`.  
> The `data/` directory is excluded from version control via `.gitignore`.

---

## Project Structure

```
olist-retail-analytics/
│
├── data/
│   ├── raw/                        # CSV files from Kaggle (gitignored)
│   └── processed/                  # intermediate outputs (gitignored)
│
├── schema/
│   ├── star_schema.sql             # DDL — CREATE TABLE statements
│   └── star_schema.png             # ERD diagram
│
├── pipeline/
│   └── etl.py                      # Load CSV → PostgreSQL
│
├── analysis/
│   ├── revenue_trends.sql          # Monthly GMV and order volume
│   ├── seller_performance.sql      # Seller ranking by GMV and review score
│   ├── customer_cohort.sql         # Monthly cohort retention
│   └── regional_breakdown.sql     # Revenue and orders by state
│
├── dashboard/
│   └── screenshots/                # Dashboard preview images
│
├── notebooks/
│   └── eda.ipynb                   # Exploratory data analysis
│
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Star Schema Design

The data is modeled into a **star schema** centered on orders as the primary fact.

```
                    dim_customers
                         │
dim_sellers ── fact_orders ── dim_products
                         │
                    dim_payments
                    dim_reviews
                    dim_date
```

`fact_orders` stores one row per order item, with foreign keys to all dimension tables and measurable facts: `price`, `freight_value`, `review_score`, and delivery metrics.

See `schema/star_schema.sql` for full DDL and `schema/star_schema.png` for the ERD.

---

## Key Analyses

| File | Description |
|---|---|
| `revenue_trends.sql` | Monthly GMV, order count, and average order value with MoM growth using window functions |
| `seller_performance.sql` | Seller ranking by GMV, order volume, and weighted review score using `RANK()` and CTEs |
| `customer_cohort.sql` | Monthly cohort retention — percentage of customers returning in subsequent months |
| `regional_breakdown.sql` | Revenue, order volume, and average freight cost broken down by Brazilian state |

---

## Dashboard Preview

> Screenshots available in `dashboard/screenshots/`.

The dashboard covers four main views:
- **Executive Summary** — GMV trend, total orders, average order value
- **Seller Leaderboard** — Top performers by revenue and review score
- **Cohort Retention** — Heatmap of monthly customer retention rates
- **Regional Map** — Order density and revenue by state

---

## Tech Stack

| Layer | Tool |
|---|---|
| Language | Python 3.10+ |
| ETL | pandas, SQLAlchemy, psycopg2 |
| Database | PostgreSQL 15 |
| Analysis | SQL (CTEs, window functions, aggregations) |
| Dashboard | Tableau Public / Apache Superset |
| Notebook | Jupyter |

---

## How to Run

### 1. Prerequisites

- Python 3.10+
- PostgreSQL 15 installed and running locally
- Dataset downloaded from Kaggle (see link above)

### 2. Clone the repo

```bash
git clone https://github.com/FullKersa/olist-retail-analytics.git
cd olist-retail-analytics
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Setup the database

Create a PostgreSQL database:

```sql
CREATE DATABASE olist_analytics;
```

Create the schema:

```bash
psql -U postgres -d olist_analytics -f schema/star_schema.sql
```

### 5. Configure environment

Copy the example env file and fill in your Postgres credentials:

```bash
cp .env.example .env
```

```
DB_HOST=localhost
DB_PORT=5432
DB_NAME=olist_analytics
DB_USER=postgres
DB_PASSWORD=your_password
```

### 6. Run the ETL

```bash
python pipeline/etl.py
```

This loads all CSVs from `data/raw/` and populates the star schema tables.

### 7. Run the analysis queries

Open any `.sql` file in `analysis/` and run against the `olist_analytics` database using your preferred client (psql, DBeaver, DataGrip, etc.).

---

## Author

**Bintang Muhammad**  
BI Analyst & Engineer · [LinkedIn](https://linkedin.com/in/bintangmuhammad) · [GitHub](https://github.com/FullKersa) · [Portfolio](https://bintang-bi-fullkersa.vercel.app)