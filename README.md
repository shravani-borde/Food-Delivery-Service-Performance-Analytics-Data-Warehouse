# 🍔 Food Delivery Service Performance Analytics Data Warehouse

## Project Overview

Online food delivery platforms generate large volumes of operational data such as customer orders, delivery timelines, restaurant performance metrics and customer behaviour information.
These datasets are often stored in isolated systems, making integrated performance analysis and strategic decision-making difficult.

This project focuses on designing and implementing an **ETL-based analytical data warehouse** to consolidate food delivery datasets from multiple sources and generate meaningful business insights.

The system enables analysis of delivery efficiency, demand trends, restaurant performance and customer behaviour using structured data warehousing techniques.

---

## 🎯 Objectives

* Design ETL pipelines to integrate multi-source food delivery datasets
* Build a centralized analytical data warehouse
* Perform data cleaning, transformation and enrichment
* Enable performance analysis using structured data models
* Support data-driven operational and managerial decisions


---

## Tech Stack

| Layer | Tool |
|---|---|
| EDA & Cleaning | Google Colab (Python / Pandas) |
| ETL Pipeline | Apache NiFi 2.8.0 |
| Data Warehouse | MySQL 8.0 |
| Dashboards | Apache Superset |

---

## Project Architecture

```
Raw CSVs (Zomato, Swiggy, Delivery, Customers)
        │
        ▼
Google Colab — EDA + Cleaning
        │
        ▼
Clean CSVs → Apache NiFi ETL Pipeline
        │
        ▼
MySQL Data Warehouse (Star Schema)
        │
        ▼
Apache Superset Dashboards
```

---

## Data Sources

| Dataset | Rows | Description |
|---|---|---|
| Zomato | 7,105 | Restaurant listings, ratings, cuisine types — Bangalore |
| Swiggy | 8,680 | Restaurant listings, delivery time estimates — Bangalore |
| Food Delivery Time | 1,000 | Actual courier delivery metrics, weather, traffic |
| Online Food Orders | 388 | Customer demographics, income, feedback |

---

## Database Schema (Star Schema)

### Fact Table

**Fact_Orders**
- `order_id` — PK
- `rider_id` — FK → Dim_Rider
- `distance_km`
- `weather`
- `traffic_level`
- `time_of_day`
- `vehicle_type`
- `preparation_time_mins`
- `experience_yrs`
- `delivery_time_mins`
- `delay_flag` — derived column (1 if delivery > 45 mins)

### Dimension Tables

**Dim_Restaurant** (15,785 rows — Zomato + Swiggy merged)
- `restaurant_id`, `name`, `restaurant_type`, `cuisine_type`
- `avg_rating`, `num_ratings`, `avg_cost_two`
- `online_order`, `table_booking`
- `area`, `address`, `city`, `source`

**Dim_Customer** (388 rows)
- `customer_id`, `age`, `gender`, `marital_status`
- `occupation`, `monthly_income`, `education`, `family_size`
- `latitude`, `longitude`, `pin_code`
- `orders_online`, `feedback`

**Dim_Rider** (30 rows)
- `rider_id`, `vehicle_type`, `experience_yrs`
- `avg_delivery_time`, `total_deliveries`

**Dim_Time** (4 rows)
- `time_id`, `time_of_day`, `hour`, `is_weekend`

**Dim_Location** (388 rows)
- `location_id`, `latitude`, `longitude`, `pin_code`, `area`, `city`

---

## Warehouse Row Counts

| Table | Rows |
|---|---|
| dim_time | 4 |
| dim_customer | 388 |
| dim_rider | 30 |
| dim_location | 388 |
| dim_restaurant | 15,785 |
| fact_orders | 1,000 |

---

## EDA & Cleaning Summary

All cleaning was performed in Google Colab before loading into NiFi.

| Dataset | Nulls Fixed | Action Taken |
|---|---|---|
| Zomato | 68 rating nulls, 57 cost nulls | Filled with median (3.5 and 400.0) |
| Zomato | 2 unnamed index columns | Dropped |
| Zomato | Yes/No columns | Encoded to 1/0 |
| Swiggy | 0 nulls | No action needed |
| Delivery | 30 Weather nulls | Filled with mode ("Clear") |
| Delivery | 1 Experience null | Filled with median (5.0) |
| Customers | 0 nulls | Encoded Output and Feedback to 1/0 |

### Key Derived Column
`delay_flag = 1 if Delivery_Time_min > 45 else 0`

**Finding:** 664 out of 1000 orders (66.4%) were delayed — the central KPI of the dashboard.

---

## NiFi ETL Flow

Each table has its own GetFile → PutDatabaseRecord flow:

```
GetFile (reads CSV from input folder)
    │
    ▼
PutDatabaseRecord (inserts into MySQL table)
```

### Controller Services
- **CSVReader** — Schema Access Strategy: `Use String Fields From Header`
- **DBCPConnectionPool** — connects to `food_dw` MySQL database

### Load Order (dimension tables before fact table)
1. dim_time
2. dim_customer
3. dim_rider
4. dim_location
5. dim_restaurant
6. fact_orders ← last (FK dependencies)

---

## Setup & Installation

### Prerequisites
- Java 21 (`C:\Program Files\Java\jdk-21`)
- MySQL 8.0
- Apache NiFi 2.8.0
- Docker Desktop (for Superset)

### Start NiFi
```bash
cd C:\nifi\nifi-2.8.0\bin
nifi.cmd start
```
Access at: `https://localhost:8443/nifi`

### Start MySQL
```bash
net start MySQL80
```

### Start Superset (Docker)
```bash
docker start superset
```
Access at: `http://localhost:8088`

### MySQL Connection String (for Superset)
```
mysql+pymysql://root:YOUR_PASSWORD@host.docker.internal:3306/food_dw
```

---

## Dashboard KPIs

| KPI | Description |
|---|---|
| Total Orders | COUNT of all orders |
| Avg Delivery Time | AVG(delivery_time_mins) |
| Late Delivery % | AVG(delay_flag) × 100 |
| Cuisine Share | COUNT grouped by cuisine_type |
| Peak Demand Hours | COUNT grouped by time_of_day |
| Region Heatmap | COUNT grouped by area/zone |
| Payment Methods | COUNT grouped by monthly_income proxy |

---

## Key Insights from EDA

- **66.4% of orders are delayed** (delivery > 45 mins) — major operational issue
- **Clear weather dominates** — 47% of deliveries happen in clear weather
- **77.6% of customers order online** — strong digital adoption
- **81.7% customer feedback is positive**
- Swiggy delivery time (mean 54 min) vs actual measured time (mean 57 min) — close but Swiggy slightly underestimates
- Zomato dataset is entirely Bangalore — both platforms cover the same city enabling valid comparison

---

## Project Structure

```
project/
│
├── colab/
│   └── eda_cleaning.ipynb          — EDA and cleaning notebook
│
├── data/
│   ├── raw/
│   │   ├── zomato.csv
│   │   ├── swiggy.csv
│   │   ├── food_delivery_time.csv
│   │   └── online_food.csv
│   └── clean/
│       ├── dim_restaurant.csv
│       ├── dim_customer.csv
│       ├── dim_rider.csv
│       ├── dim_time.csv
│       ├── dim_location.csv
│       └── fact_orders_ready.csv
│
├── mysql/
│   └── create_tables.sql           — All CREATE TABLE statements
│
└── README.md
```

---

## Notes

- Zomato and Swiggy are **UNIONed** (not JOINed) in `dim_restaurant` — no shared natural key exists between platforms. Each row has a `source` column (`'zomato'` or `'swiggy'`) for origin tracking.
- `dim_time` only has 4 rows (Morning, Afternoon, Evening, Night) — the source dataset used time-of-day buckets instead of exact timestamps.
- `delay_flag` threshold of 45 minutes was chosen based on industry standard SLA for food delivery.
- NiFi CSVReader must use `Use String Fields From Header` schema strategy to handle mixed-type columns in `dim_restaurant`.
