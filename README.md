# 🛒 Retail Insights Platform - Azure Data Engineering Project

Welcome to the **Retail Insights Platform**, a complete end-to-end Azure-based data engineering project designed to simulate a real-world enterprise-grade data pipeline. This README is written assuming you're a junior or intermediate data engineer who has some basic understanding of Azure Data Factory, Azure Databricks, Azure Synapse, and Python, but may not be fully confident using them all together yet. Don't worry—this will walk you through everything!

---

## 🎯 Project Objective

Ingest, clean, transform, and analyze multi-source sales data (CSV, JSON, Parquet) from an on-prem SQL Server system using the following Azure services:

- **Azure Data Factory** for ETL & orchestration
- **Azure Data Lake Storage Gen2** as the central data lake
- **Azure Databricks** (Delta Lake) for transformation & optimization
- **Azure Synapse Analytics** for querying and reporting
- **Azure Monitor** for observability
- **Azure DevOps** for CI/CD automation

---

## 🧠 Architecture Overview

```text
        +--------------------+
        |  SQL Server (VM)   |
        +--------+-----------+
                 |
     (SHIR + Copy Data Activity)
                 ↓
+-----------------------------------+
|   Azure Data Factory (ADF)        |
|  + Linked Services               |
|  + Pipelines (parametrized)     |
+----------------+-----------------+
                 ↓
      +--------------------+
      |   Azure Data Lake  |
      |      Storage Gen2  |
      |  (raw / clean / curated) |
      +--------------------+
                 ↓
    +-----------------------------+
    | Azure Databricks Notebooks |
    |   - Read, Clean, Merge     |
    |   - Delta Lake Write       |
    +-----------------------------+
                 ↓
   +-----------------------------+
   | Azure Synapse Analytics     |
   | - Dedicated & Serverless    |
   | - External Tables + Views   |
   +-----------------------------+
                 ↓
          [ Power BI Optional ]
                 ↓
     +--------------------------+
     | Azure Monitor + Alerts   |
     +--------------------------+
                 ↓
     +--------------------------+
     | Azure DevOps Pipelines   |
     | - ARM + YAML Deployments |
     +--------------------------+
```

---

## 📂 Directory Structure (GitHub Style)

```text
retail-insights-platform/
├── data/
│   ├── stores.csv        # 2000+ rows
│   ├── products.json     # 2000+ entries
│   └── sales_stream.parquet # streaming-ready
├── sql/
│   └── generate_mock_data.sql
├── notebooks/
│   └── retail_cleaning_etl.ipynb
├── adf/
│   ├── linkedServices.json
│   ├── pipelines.json
│   └── datasets.json
├── synapse/
│   └── external_tables.sql
├── devops/
│   ├── arm_templates/
│   │   └── infrastructure.json
│   └── pipelines/
│       └── deploy.yml
├── docs/
│   └── architecture_diagram.drawio
└── README.md
```

---

## 🛠 Prerequisites

You will need the following before starting:

1. An active **Azure subscription**
2. Basic understanding of:
   - SQL Server and T-SQL
   - Azure Data Factory (ADF)
   - Azure Data Lake Storage Gen2
   - Azure Databricks & PySpark
   - Synapse SQL Pools
   - Azure DevOps Pipelines

---

## 🧱 Step-by-Step Implementation Guide

### 1️⃣ Setup SQL Server & Mock Data
- Launch an **Azure VM** or use local SQL Server
- Use the script in `sql/generate_mock_data.sql` to create and populate:
  - `Stores`
  - `Products`
  - `Sales`

### 2️⃣ Configure Azure Data Factory (ADF)
- Create **Linked Services** for:
  - SQL Server (via SHIR)
  - ADLS Gen2
  - Azure Key Vault
- Use `pipelines.json` and `datasets.json` to import into ADF
- Configure **Triggers**:
  - Schedule (daily)
  - Tumbling Window (for batch)

### 3️⃣ Upload Raw Files to ADLS Gen2
- Create three containers in the ADLS Gen2:
  - `raw`
  - `clean`
  - `curated`
- Folder structure:
```text
abfs://raw/retail/stores/YYYY/MM/DD/
abfs://raw/retail/products/YYYY/MM/DD/
abfs://raw/retail/sales/YYYY/MM/DD/
```

### 4️⃣ Launch Azure Databricks
- Import the notebook from `notebooks/retail_cleaning_etl.ipynb`
- Tasks:
  - Read from `raw/`
  - Validate + clean
  - Write to Delta tables in `clean/`
  - Upsert sales using `MERGE INTO`

### 5️⃣ Query with Synapse Analytics
- Create **Serverless SQL Pool**:
  - Use `CREATE EXTERNAL TABLE` from `external_tables.sql`
- Create **Dedicated Pool**:
  - Load `curated` data into fact and dimension tables
- Create **Materialized Views** for top-selling products

### 6️⃣ Enable Azure Monitoring
- Diagnostic Settings:
  - ADF Pipeline Failures
  - Databricks Job Duration
- Alerts:
  - Cluster long run
  - Failed pipeline retry alert
- Use Azure Monitor + Log Analytics

### 7️⃣ CI/CD with Azure DevOps
- Push all code to GitHub or Azure Repo
- Import ARM templates in `devops/arm_templates/`
- Configure pipeline using `deploy.yml`
- Deploy to Dev, QA, and Prod environments

---

## 📊 Sample Data Overview

| File | Type | Rows | Columns |
|------|------|------|---------|
| stores.csv | CSV | 2,000+ | store_id, name, city, state |
| products.json | JSON | 2,000+ | product_id, name, category, price |
| sales_stream.parquet | Parquet | Simulated streaming | sale_id, store_id, product_id, quantity, sale_date |

---

## 💻 Databricks ETL Notebook Highlights
- `read.csv()` + `read.json()` + `read.parquet()`
- `df.dropna()`, `df.filter()`
- `df.write.format("delta")`
- `MERGE INTO sales_delta USING new_sales` logic
- `OPTIMIZE`, `ZORDER`, and `VACUUM`

---

## 🎨 Diagram
See `docs/architecture_diagram.drawio` for the full IO flow and component interaction.

---

## 📚 Learning Outcomes
- Practical experience in deploying ADF pipelines
- Understand modular lakehouse architecture
- Optimize Delta tables and manage schema evolution
- Query Delta tables using Synapse SQL Pools
- CI/CD automation with DevOps pipelines
- Enterprise monitoring with alerting & dashboards

---

## 🧹 Cleanup
To avoid Azure charges, delete the following when done:
- Databricks Workspace
- ADF + SHIR
- ADLS Storage
- Synapse Pools
- Log Analytics Workspace

---

## ❓Need Help?
Open issues on GitHub or ask for detailed walkthroughs per module.

You got this 💪 — let’s build cloud data pipelines like a pro!

