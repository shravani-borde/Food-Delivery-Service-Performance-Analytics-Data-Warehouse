# 🍔 Food Delivery Service Performance Analytics Data Warehouse

## 📌 Project Overview

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

## 🛠️ Technology Stack

* **ETL Tool:** Apache NiFi
* **Data Warehouse:** MySQL
* **Programming / Processing:** SQL
* **Data Source:** Public datasets (Kaggle)

---

## 📂 Datasets Used

The project integrates multiple datasets to simulate real-world data engineering challenges.

### 1️⃣ Food Delivery Time Dataset

Contains operational delivery metrics such as:

* Order ID
* Distance
* Traffic level
* Weather conditions
* Preparation time
* Delivery time
* Vehicle type

This dataset forms the **Fact table** for delivery performance analytics.

### 2️⃣ Restaurant Datasets (Zomato + Swiggy)

Includes restaurant attributes such as:

* Restaurant name
* Cuisine type
* Ratings
* Cost information
* Location (area / city)

These datasets are integrated to create the **Restaurant Dimension table**.

### 3️⃣ Online Food Ordering Dataset

Contains customer demographic and behavioural data:

* Age
* Gender
* Occupation
* Monthly income
* Family size
* Educational qualification
* Feedback

This dataset is used to design the **Customer Dimension table**.

---

## 🏗️ Data Warehouse Design

A **Star Schema** is implemented consisting of:

* **Fact_Orders** – delivery performance metrics
* **Dim_Restaurant** – restaurant information
* **Dim_Customer** – customer demographic details
* **Dim_Time** – order time categorization

This schema supports efficient analytical querying and reporting.

---

## 🔄 ETL Process

The ETL workflow developed using Apache NiFi includes:

### Extract

* Import raw CSV datasets from multiple sources

### Transform

* Remove irrelevant columns
* Handle missing values
* Standardize data formats
* Generate surrogate keys
* Derive analytical attributes such as total delivery time and delay indicators
* Integrate restaurant datasets

### Load

* Populate structured fact and dimension tables in MySQL data warehouse

---

## 📊 Analytical Capabilities

The data warehouse enables analysis such as:

* Peak ordering period identification
* Delivery delay pattern analysis
* Restaurant rating and cost comparison
* Distance vs delivery efficiency study
* Customer demographic behaviour trends
* Regional demand insights

---

## 💡 Business Value

The system supports decision-making related to:

* Delivery resource allocation
* Operational efficiency improvement
* Restaurant partnership strategy
* Demand forecasting
* Customer engagement planning

---

## 🚀 Future Enhancements

* Real-time data ingestion
* Interactive BI dashboards
* Predictive analytics for delivery time estimation
* Demand forecasting models
* Integration with live API data

---

## 📚 Academic Context

This project is developed as part of a **Data Warehousing / ETL academic study**, demonstrating practical implementation of:

* Data integration
* Schema design
* ETL pipeline development
* Analytical data modelling

---
