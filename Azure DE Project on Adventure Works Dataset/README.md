# Azure Data Engineering Pipeline (Adventure Works Dataset)
 
A cloud-native ETL pipeline built on Azure that simulates an enterprise data engineering workflow. It pulls raw data from GitHub, processes it through a bronze-silver-gold medallion architecture, models it as a star schema in Azure Synapse Analytics, and serves it to Power BI dashboards.
 
## Overview
 
The goal of this project was to practice the full data engineering lifecycle on Azure: ingestion, transformation, warehousing, and reporting. The [Adventure Works dataset](https://www.kaggle.com/datasets/ukveteran/adventure-works) (multi-table sales and customer data, available on Kaggle) is ingested automatically through an API, cleaned and joined with PySpark, modeled for analytics, and visualized in Power BI.
 
## Architecture and Pipeline Steps
 
```
GitHub (HTTP) -> Data Factory -> ADLS Gen2 Bronze -> Databricks (PySpark) -> ADLS Gen2 Silver -> Synapse (Gold) -> Power BI
```
 
1. **Data source and ingestion (Azure Data Factory)**
   - ADF pipelines pull the Adventure Works data from GitHub over HTTP
   - Ingestion is fully automated and lands the files in Azure Data Lake Storage Gen2
2. **Bronze: raw data store (ADLS Gen2)**
   - Raw data is stored exactly as received, so the original source data is always available
3. **Silver: transformation (Databricks and PySpark)**
   - Databricks reads the raw data, cleanses it, and joins the multi-table sales and customer datasets
   - The transformed data is written back to ADLS Gen2 as the silver layer
4. **Gold: serving (Azure Synapse Analytics)**
   - Curated, analytics-ready data is loaded into Azure Synapse Analytics (SQL Pools)
   - Designed a star schema to speed up queries and reporting
   - Created external tables and `OPENROWSET` queries for flexible access to the data
5. **Reporting (Power BI)**
   - Connected Synapse to Power BI and built dashboards that track sales KPIs and operational trends
## Tech Stack
 
| Area | Tools |
|---|---|
| Orchestration and ingestion | Azure Data Factory (ADF) |
| Storage | Azure Data Lake Storage Gen2 (ADLS Gen2) |
| Processing | Azure Databricks, PySpark |
| Warehousing | Azure Synapse Analytics (SQL Pools) |
| Modeling and querying | Star schema, SQL, external tables, OPENROWSET |
| Visualization | Power BI |
 
## What I Learned
 
- Building and automating ingestion pipelines with ADF
- Organizing a data lake with a medallion architecture
- Cleaning and joining multi-table data at scale with PySpark
- Designing a star schema for reporting workloads
- Connecting a data warehouse to a BI tool for end users
