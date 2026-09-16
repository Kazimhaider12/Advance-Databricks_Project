# 🏎️ End-to-End F1 Race Telemetry & Data Engineering Pipeline

```markdown

A production-grade Data Engineering pipeline built on **Databricks** utilizing the **Medallion Architecture (Bronze → Silver → Gold)**. This project ingests raw Formula 1 datasets, performs cleaning, joins, and aggregations using **Delta Live Tables (DLT)** and PySpark, and delivers actionable race insights through an interactive **Databricks Lakeview Dashboard**.

---

## 📐 Architecture Overview

The pipeline follows the Lakehouse Medallion Architecture pattern to ensure scalability, data quality, and optimized query performance for analytics:


```

[ Raw CSV Files ]
│
▼
🥉 BRONZE LAYER  (Raw Streaming Ingestion via Auto Loader)
│
▼
🥈 SILVER LAYER  (Data Cleansing, Deduplication, Composite Joins)
│
▼
🥇 GOLD LAYER    (Business Aggregations, Telemetry Metrics, Gold Tables)
│
▼
📊 DATABRICKS DASHBOARD (Interactive Visualizations & Strategy Insights)

```
<img width="1280" height="390" alt="image" src="https://github.com/user-attachments/assets/0a3aed86-e379-4a0a-8704-5a345e1cb640" />
### 1. 🥉 Bronze Layer (Ingestion)
* Ingests raw F1 data (`drivers`, `results`, `races`, `constructors`, `lap_times`, `circuits`) from Azure Data Lake Storage (ADLS) using Databricks **Auto Loader (`cloudFiles`)**.
* Stores raw records as **Delta Lake** tables preserving schema and history.

### 2. 🥈 Silver Layer (Cleansing & Transformations)
* Handles data cleansing, schema enforcement, data type casting, and deduplication.
* Implements **Delta Live Tables Expectations** (`@dlt.expect_or_drop`) to ensure strict data quality standards by dropping invalid NULL keys.

### 3. 🥇 Gold Layer (Analytical Aggregations)
* Combines multi-entity datasets via `LEFT OUTER JOIN` queries across composite keys (`race_id`, `driver_id`).
* Builds high-performance business views including driver lap-by-lap telemetry, fastest lap timing, average positions, and constructor metrics.

---

## 🛠️ Tech Stack & Skills

* **Cloud Platform:** Azure Databricks, ADLS Gen2
* **Storage & Format:** Delta Lake (ACID Transactions)
* **Pipeline Framework:** Delta Live Tables (DLT), Databricks Workflows (Jobs)
<img width="1648" height="777" alt="image" src="https://github.com/user-attachments/assets/8dfed24e-dbda-476f-9a5a-25103e5c2fc0" />
* **Processing Engine:** Apache Spark (PySpark), Spark SQL
* **Visualization:** Databricks Lakeview Dashboards
* **Data Quality:** DLT Expectations (`expect_or_drop`)

---

## 📊 Key Dashboard Insights

<img width="1514" height="804" alt="image" src="https://github.com/user-attachments/assets/5e91f930-29a9-4bc6-8814-8ea1fe755c32" />
<img width="1499" height="564" alt="image" src="https://github.com/user-attachments/assets/e451350a-0206-4608-a2d2-dbf091917a38" />

The final Gold Layer dataset powers the **F1 Global Race Performance & Telemetry Hub** dashboard, featuring:

1. **Driver Race Pace & Progression:** Line chart tracking position changes lap-by-lap to analyze pit-stop strategies and overtakes.
2. **Constructor Efficiency:** Performance evaluation of teams across seasons based on podium finishes and total completed laps.
3. **Grid Representation:** Donut and Pie charts breaking down driver nationalities and constructor reliability metrics.
4. **Geographical Performance:** Circuit-level historical analysis of driver finishing positions.

---

```

---

## 🚀 How to Run the Pipeline

1. **Setup Databricks Environment:** Connect your Azure Databricks workspace with Azure Blob Storage / ADLS Gen2.
2. **Import Notebooks:** Clone this repository into your Databricks Workspace folder.
3. **Create DLT Pipeline:**
* Navigate to **Delta Live Tables** under *Workflows*.
* Add the paths for `01_Ingestion_Bronze`, `02_Transformation_Silver`, and `03_Aggregate_Gold` notebooks.
* Set target schema (e.g., `default` or custom catalog).


4. **Trigger Pipeline:** Click **Start** (or **Full Refresh**) to run the end-to-end Medallion DAG execution.

```

```
