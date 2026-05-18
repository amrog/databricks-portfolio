# Azure Databricks Data Engineering Portfolio

A progressive series of six data engineering projects built on Azure Databricks, demonstrating end-to-end proficiency across the Databricks lakehouse platform. Each project builds on the last — from raw ingestion to production-grade pipelines with CI/CD and cost governance.

> Built by a data engineer with production experience in Microsoft Fabric, this portfolio explicitly demonstrates platform breadth and the ability to own a Databricks environment — not just use it.

---

## Tech Stack

| Layer | Tools |
|---|---|
| Compute | Azure Databricks (14.3 LTS, Spark 3.5.0) |
| Storage format | Delta Lake |
| Governance | Unity Catalog |
| Orchestration | Databricks Workflows + dbt Core |
| Streaming | Autoloader + Structured Streaming |
| ML | MLflow + Databricks Feature Store |
| IaC | Terraform + Databricks Asset Bundles |
| CI/CD | GitHub Actions |
| Cloud | Azure (West US) |

---

## Projects

### Project 01 — Medallion Lakehouse on Delta Lake
**Status:** Complete

A production-style Bronze → Silver → Gold lakehouse pipeline built on the NYC Taxi Trip Records dataset (January 2023, 3M+ rows).

**What was built:**
- Bronze layer: raw Parquet ingestion to Delta with metadata columns (`_ingestion_ts`, `_source_file`) partitioned by `trip_date`
- Silver layer: data quality filtering (185,392 bad records removed — 6% of raw data), type casting, derived `trip_duration_mins` column, deduplication via `MERGE INTO`
- Gold layer: three business-ready aggregate tables — hourly revenue by pickup zone, average trip duration by day of week, top 10 pickup locations by volume

**Delta Lake features demonstrated:**
- `DESCRIBE HISTORY` — full transaction log across all three layers
- `OPTIMIZE` + `ZORDER BY (PULocationID, pickup_hour)` — compacted 68 files → 34 files (50% reduction)
- `VERSION AS OF` — time travel query to recover pre-transformation state
- `VACUUM` — file retention management with configurable retention window

**Key metrics:**
```
Bronze:  3,066,766 rows   68 files   partitioned by trip_date
Silver:  2,881,374 rows   185,392 records failed quality checks (6%)
Gold:    3 aggregate tables   34 files post-OPTIMIZE   50% file compaction
```

**Fabric vs Databricks:**
In Microsoft Fabric, OPTIMIZE and VACUUM run automatically as background platform processes — the engineer has no visibility or control. In Databricks, these are explicit operations owned by the engineer. This project demonstrates direct management of the Delta transaction log, file compaction strategy, and retention policy — the operational ownership that separates a Fabric user from a Databricks practitioner.

**Notebooks:**
```
project-01-medallion/
├── 01_ingest_bronze.py
├── 02_transform_silver.py
├── 03_aggregate_gold.py
└── 04_delta_features_demo.py
```

---

### Project 02 — Databricks SQL + Unity Catalog
**Status:** Coming soon

Enterprise governance layer built on top of Project 01 Gold tables. Demonstrates Unity Catalog's three-level namespace (catalog → schema → table), column masking, row-level security, data lineage, and audit logging via system tables.

**Planned deliverables:**
- `prod_catalog` and `dev_catalog` with bronze/silver/gold schemas
- Column masking on `payment_type` using SQL functions
- Row-level filters restricting analysts to their assigned borough
- Lineage graph screenshot from Catalog Explorer
- Databricks SQL dashboard on Gold tables
- Audit log queries against `system.access.audit`

---

### Project 03 — Autoloader + Structured Streaming
**Status:** Coming soon

End-to-end streaming pipeline using Databricks Autoloader for cloud file ingestion with schema inference, feeding into a Structured Streaming job with stateful aggregations.

---

### Project 04 — Databricks Workflows + dbt Core
**Status:** Coming soon

Code-first orchestration using Databricks Workflows as the scheduler and dbt Core running on Databricks SQL for transformation logic. Includes job dependencies, dbt tests, and auto-generated documentation.

---

### Project 05 — MLflow + Feature Store ML Pipeline
**Status:** Coming soon

End-to-end ML pipeline with experiment tracking in MLflow, feature engineering backed by the Databricks Feature Store, model registration, and serving via Databricks Model Serving.

---

### Project 06 — Production Platform: CI/CD, Monitoring + Cost Governance
**Status:** Coming soon

Capstone project wiring everything together with Databricks Asset Bundles for CI/CD, Terraform for infrastructure-as-code, and System Tables for cost dashboards and observability.

---

## Dataset

**NYC Taxi Trip Records — Yellow Cab**
- Source: [NYC Taxi & Limousine Commission](https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page)
- Format: Parquet
- Coverage: January 2023 (3,066,766 trips)
- Key fields: pickup/dropoff timestamps, location IDs, fare amounts, trip distance, passenger count

---

## Environment

```
Workspace:    churn-ml-workspace (Azure, West US)
Runtime:      Databricks 14.3 LTS (Spark 3.5.0, Scala 2.12)
Cluster:      Standard_D4_v2 — Single node, 28GB, 8 cores
Cost:         1.5 DBU/h with 20-minute auto-termination
```

---

## Background

This portfolio was built by a data engineer with production experience on the Microsoft Fabric stack (6 completed projects). The Databricks portfolio was built deliberately to demonstrate cross-platform depth — the ability to work across both major enterprise lakehouse platforms and articulate the trade-offs between them.

| Capability | Microsoft Fabric | Azure Databricks |
|---|---|---|
| Table format | Delta Lake (managed) | Delta Lake (explicit control) |
| Governance | Microsoft Purview | Unity Catalog |
| File optimization | Automatic (background) | Manual OPTIMIZE + ZORDER |
| Audit logs | Azure Monitor | system.access.audit table |
| Orchestration | Data Factory pipelines | Workflows + dbt |
| Streaming | Eventstream | Autoloader + Structured Streaming |
| ML integration | Fabric ML (preview) | MLflow + Feature Store (mature) |

---

## Running the Projects

Each project folder contains numbered notebooks that run in sequence. All notebooks are self-contained and connect to `dbfs:/portfolio/` as the root storage path.

**Prerequisites:**
- Azure Databricks workspace (Premium tier for Unity Catalog projects)
- Databricks runtime 14.3 LTS or higher
- Unity Catalog metastore assigned to workspace (Projects 02+)

---

*Portfolio in active development — projects added sequentially as completed.*
