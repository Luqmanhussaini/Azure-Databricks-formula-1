# Cloud AI Data Platform — End-to-End ETL & Data Engineering

> Scalable data pipeline on Azure using PySpark and Databricks with a multi-layer medallion architecture (Bronze → Silver → Gold), designed to feed downstream AI/ML model training and evaluation workflows.

---

## Overview

This project implements a production-style cloud data engineering pipeline on **Azure Data Lake Storage Gen2**, orchestrated through **Azure Data Factory** and processed with **PySpark on Databricks**. The pipeline follows the **medallion architecture** pattern — raw data lands in Bronze, is cleaned and standardised in Silver, and aggregated into analytics-ready Gold tables that serve downstream machine learning pipelines.

Automated data quality checks are applied at each layer to catch schema drift, null violations, and value anomalies before data moves downstream.

---

## Architecture

```
Raw Sources
    │
    ▼
┌─────────────────────────────────────────────────────┐
│                  Azure Data Factory                 │
│            (Orchestration & Scheduling)             │
└─────────────────────────────────────────────────────┘
    │
    ▼
┌──────────┐    ┌──────────┐    ┌──────────┐
│  BRONZE  │───▶│  SILVER  │───▶│   GOLD   │
│  Raw     │    │ Cleaned  │    │Aggregated│
│  Ingest  │    │Validated │    │ ML-Ready │
└──────────┘    └──────────┘    └──────────┘
    │                │                │
    └────────────────┴────────────────┘
              Azure Data Lake Gen2
                (Delta Lake)
                    │
                    ▼
         Downstream AI/ML Pipelines
         (Model Training & Evaluation)
```

---

## Tech Stack

| Component | Technology |
|---|---|
| Cloud Storage | Azure Data Lake Storage Gen2 |
| Processing Engine | Apache Spark (PySpark) on Databricks |
| Orchestration | Azure Data Factory |
| Table Format | Delta Lake |
| Language | Python (PySpark), SQL |
| Data Quality | Custom validation framework (PySpark) |

---

## Pipeline Layers

### 🥉 Bronze — Raw Ingestion
- Ingests raw data from source systems as-is
- Preserves original schema and values with no transformation
- Adds metadata columns: `_ingestion_timestamp`, `_source_file`, `_batch_id`
- Stores in Delta Lake format for ACID compliance and time travel

### 🥈 Silver — Cleansing & Standardisation
- Applies schema enforcement and type casting
- Handles null values, deduplication, and format standardisation
- Runs automated data quality checks (completeness, uniqueness, range validation)
- Flags and isolates records failing quality thresholds for review
- Produces validated, joined datasets ready for feature engineering

### 🥇 Gold — Aggregation & ML Readiness
- Builds feature tables and aggregated views for ML consumption
- Applies business logic and domain-specific transformations
- Optimised for read performance (Z-ordering, partitioning)
- Directly consumed by downstream model training and evaluation pipelines

---

## Data Quality Framework

Automated checks run at each layer transition:

- **Completeness** — no nulls in required fields
- **Uniqueness** — no duplicate primary keys
- **Validity** — values within expected ranges and formats
- **Consistency** — referential integrity across joined tables
- **Threshold Alerting** — pipeline fails and raises alert if quality score drops below defined threshold

---

## Project Structure

```
cloud-ai-data-platform/
│
├── notebooks/
│   ├── 01_bronze_ingestion.py       # Raw data ingestion to Bronze layer
│   ├── 02_silver_transformation.py  # Cleansing and validation to Silver
│   ├── 03_gold_aggregation.py       # Feature building for ML (Gold)
│   └── 04_data_quality_checks.py    # Reusable quality validation functions
│
├── pipelines/
│   └── adf_pipeline_config.json     # Azure Data Factory pipeline definition
│
├── config/
│   └── quality_thresholds.yaml      # Configurable quality check parameters
│
├── docs/
│   ├── data_lineage.md              # End-to-end data lineage documentation
│   ├── transformation_logic.md      # Transformation rules per layer
│   └── pipeline_architecture.md    # Architecture decisions and trade-offs
│
└── README.md
```

---

## Key Results

- Built a fully automated, end-to-end data pipeline from raw ingestion to ML-ready feature tables
- Implemented multi-layer data quality validation with automated alerting on threshold violations
- Produced structured technical documentation covering data lineage, transformation logic, and pipeline architecture for team handover
- Pipeline designed to serve downstream AI model training and evaluation processes reliably

---

## Setup & Requirements

### Prerequisites
- Azure subscription with access to:
  - Azure Data Lake Storage Gen2
  - Azure Databricks workspace
  - Azure Data Factory
- Python 3.8+
- Databricks Runtime 12.0+ (includes Apache Spark 3.3)

### Configuration
1. Clone this repository
2. Update `config/quality_thresholds.yaml` with your data quality parameters
3. Import notebooks into your Databricks workspace
4. Configure the Azure Data Factory linked services to point to your ADLS Gen2 account
5. Import `pipelines/adf_pipeline_config.json` into Azure Data Factory

---

## Documentation

Full technical documentation is available in the `/docs` folder:
- **Data Lineage** — traces data from source to Gold layer
- **Transformation Logic** — documents every rule applied at Silver and Gold
- **Pipeline Architecture** — design decisions, trade-offs, and extension points

---

## Author

**Meer Luqman Hussaini**  
M.Sc. Data Science, Universität Trier  
[linkedin.com/in/luqman-hussaini](https://linkedin.com/in/luqman-hussaini) | [github.com/Luqmanhussaini](https://github.com/Luqmanhussaini)
