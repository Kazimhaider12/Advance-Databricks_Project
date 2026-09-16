# 🏎️ Formula 1 Data Engineering Pipeline

An end-to-end data engineering project built on **Databricks** using the **Medallion Architecture** (Bronze → Silver → Gold) to clean, transform, and analyze historical Formula 1 racing data (Ergast F1 dataset), culminating in an interactive analytics dashboard.

---

## 📌 Project Overview

This project ingests raw Formula 1 data (drivers, races, results, constructors, circuits, pit stops, lap times, and standings) and processes it through a structured, multi-layer pipeline to produce clean, reliable, and business-ready datasets for analysis and visualization.

**Tech Stack:** Databricks · Delta Live Tables (DLT) · PySpark · Delta Lake · SQL

---

## 🏗️ Architecture: Medallion (Bronze → Silver → Gold)



<img width="1280" height="390" alt="image" src="https://github.com/user-attachments/assets/0a3aed86-e379-4a0a-8704-5a345e1cb640" />


### 🥉 Bronze Layer — Raw Ingestion
Raw CSV files ingested as-is (no transformation) to preserve source-of-truth data.

**Source tables:** `drivers`, `races`, `results`, `circuits`, `constructors`, `pit_stops`, `lap_times`, `driver_standings`, `constructor_standings`, `qualifying`, `status`, `seasons`

### 🥈 Silver Layer — Cleaned & Standardized
Applies data quality rules and standardization:
- Converts Ergast's `\N` placeholder values to proper `NULL`
- Trims whitespace from string fields
- Casts columns to correct data types (int, float, date)
- Renames columns to consistent `snake_case` naming
- Drops records failing primary key / foreign key validation (`expect_or_drop`)
- Removes duplicate records (e.g., **1,707 duplicate rows** identified and removed from `lap_times`)
- Adds `silver_ingestion_timestamp` audit column

**Silver tables produced:**
| Table | Description |
|---|---|
| `silver_drivers` | Driver identity & biographical info |
| `silver_races` | Race event schedule & metadata |
| `silver_results` | Per-driver, per-race final results |
| `silver_circuits` | Circuit location & geographic data |
| `silver_constructors` | Team/constructor info |
| `silver_pit_stops` | Pit stop timing & duration |
| `silver_lap_times` | Lap-by-lap timing data |

### 🥇 Gold Layer — Business Insights
Aggregated, joined, and analytics-ready tables built using **joins, group-by aggregations, and window functions**, for example:

- Driver career summaries (total wins, points, average finish position)
- Constructor season performance
- Grid vs. finish position analysis (overtaking performance)
- Championship progression (running totals via window functions)
- Circuit-wise driver performance
- DNF / retirement rate analysis
- Lap consistency & fastest lap trends

---

## ⚙️ Pipeline: Delta Live Tables (DLT)


The Silver layer is implemented as a **Databricks DLT pipeline** using declarative Python decorators:

```python
@dlt.table(
    name="silver_drivers",
    comment="Cleaned drivers data with proper data types and quality rules",
    table_properties={"quality": "silver"}
)
@dlt.expect_or_drop("valid_driver_id", "driver_id IS NOT NULL")
def silver_drivers():
    return (
        dlt.read_stream("bronze_drivers")
        .select(...)
    )
```


<img width="1648" height="777" alt="image" src="https://github.com/user-attachments/assets/8dfed24e-dbda-476f-9a5a-25103e5c2fc0" />

**Key DLT features used:**
- `dlt.read_stream()` — streaming ingestion from Bronze tables
- `@dlt.expect_or_drop()` — data quality constraints with automatic row-dropping
- `table_properties={"quality": "silver"}` — layer tagging for lineage/governance
- Reusable `clean_col()` helper for consistent null-handling across all tables

Pipeline files are located in [`/pipeline`](./pipeline).

---

## 📊 Dashboard


<img width="1514" height="804" alt="image" src="https://github.com/user-attachments/assets/5e91f930-29a9-4bc6-8814-8ea1fe755c32" />
<img width="1499" height="564" alt="image" src="https://github.com/user-attachments/assets/e451350a-0206-4608-a2d2-dbf091917a38" />

An interactive dashboard built on top of the Gold layer tables, providing visual exploration of:
- Driver & constructor championship trends over time
- Race-by-race performance comparisons
- Grid-to-finish position movement
- Pit stop strategy impact
- Circuit-level performance heatmaps

*(Add dashboard screenshots / link here once published, e.g. Databricks SQL Dashboard or Power BI/Tableau link.)*

---

## 📁 Repository Structure

```
├── data/
│   └── raw/                  # Bronze source CSVs (Ergast F1 dataset)
├── pipeline/
│   └── silver_transformations.py   # DLT Silver layer pipeline
├── gold/
│   └── gold_queries.sql      # Gold layer aggregation queries
├── dashboard/
│   └── ...                   # Dashboard config / exports
└── README.md
```

---

## 🗃️ Dataset Source

This project uses the **https://www.kaggle.com/datasets/jtrotman/formula-1-race-data**, covering F1 seasons from 1950 onward, including:

`drivers` · `races` · `results` · `constructors` · `circuits` · `pit_stops` · `lap_times` · `driver_standings` · `constructor_standings` · `qualifying` · `status` · `seasons`

---

## 🚀 How to Run

1. Clone this repository
2. Upload raw CSVs to your Databricks Bronze ingestion path (Volumes / DBFS)
3. Create a **Delta Live Tables pipeline** pointing to `pipeline/silver_transformations.py`
4. Run the pipeline to generate Silver tables
5. Execute Gold layer SQL scripts in `gold/gold_queries.sql`
6. Connect the Gold tables to your dashboarding tool of choice

---

## 📈 Key Data Quality Fixes Applied

| Issue | Table | Fix |
|---|---|---|
| `\N` placeholder nulls | All tables | Converted to proper `NULL` |
| 1,707 duplicate rows | `lap_times` | Removed via deduplication |
| `duration` stored as string | `pit_stops` | Cast to numeric (float) |
| Inconsistent naming | All tables | Standardized to `snake_case` |
| Missing FK validation | `results` | Added FK null checks |

---

## 🛠️ Tools & Technologies

- **Databricks** — Lakehouse platform
- **Delta Live Tables (DLT)** — Declarative pipeline framework
- **PySpark / Apache Spark** — Data processing engine
- **Delta Lake** — Storage layer with ACID transactions
- **SQL** — Gold layer analytics queries
- **Dashboarding tool** — *(specify: Databricks SQL / Power BI / Tableau)*

---

## 👤 Author

*Kazim Haider Syed, LinkedIn, and contact info here.*

---


