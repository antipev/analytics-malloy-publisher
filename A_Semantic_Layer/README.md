# MASTER README: MALLOY SEMANTIC MODEL
# Architecture: Layered Structure (Sources -> Extended Sources -> Models)

## 1. PROJECT OVERVIEW
This project implements a modular, scalable Malloy modeling structure. 

### DEMO DATA

---
This repository contains examples of Malloy files connected to the BigQuery Public Dataset: [theLook eCommerce](https://console.cloud.google.com/marketplace/product/bigquery-public-data/thelook-ecommerce).

The folder "0_data" was created only for DEMO purposes to keep extracted data (from the BigQuery Public Dataset as parquet files) offline.

```
* Note: in "config.malloy" file you will see 3 connections:
bigquery.table - this means when Malloy is installed on VS Code, this connection should be created as per documentation: https://docs.malloydata.dev/documentation/language/connections

thelook - duckdb connection type also to be created if you decide to use local database. In this Demo "thelook.duckdb" was created from parquet files available in the "0_data" folder.

duckdb - no connection needed to handle parquet files

Therefore proper source to be used (uncommented/commented out) in config file: 

-- source: distribution_centers_raw is bigquery.table('bigquery-public-data.thelook_ecommerce.distribution_centers')
-- source: distribution_centers_raw is duckdb.table('A_Semantic_Layer/0_data/distribution_centers/*.parquet')
source: distribution_centers_raw is thelook.sql("SELECT * FROM main.distribution_centers")

```

---

By separating raw data connections from business logic and relationship definitions, we ensure the model is resilient to upstream schema changes in BigQuery.

---

## 2. THE LAYER ARCHITECTURE

### LAYER: DATA (Connection, Base)
**Location:** `/0_data/`
**Purpose:** Direct mapping to BigQuery (or selected sources/connections, like in this DEMO).
- **Rules:** No business logic, no measures, no joins.
- **Key File:** `config.malloy` (Defines `bigquery.table()` pointers).
- **Benefit:** If a table name changes in the warehouse, you only update it here once.

### LAYER: SOURCES (Staging)
**Location:** `/1_sources/`
**Purpose:** Refining raw data into usable analytical entities.
- **Rules:** Every file imports from `/0_data/` and uses the `extend` keyword.
- **Actions:** Define `primary_key`, rename technical columns to human-readable labels, and add basic dimensions, and measures. also important to add Semantic tags: defenitions of columns and measures.
`joins` also can be added to the data sources here, only if neccessary: those joins are on demand only if specific columns need to be used in the future. (Pre-joins can happen even before this stage - in dwh directly, in that case joins will not be needed here)
- **Benefit:** Creates a "clean" version of the source that is consistent across all models and reusable.

### LAYER: EXTENDED SOURCES (Curated)
**Location:** `/2_extended_sources/`
**Purpose:** Presenting analytical entities for final use.
- **Rules:** Every file imports from `/1_sources/` (or from `/3_models/` if necessary) and uses the `include` keyword.
- **Actions:** Define columns to be presented for each source.
This is important if `joins` were performed on previous stage (`/1_sources/` or on data modeling stage: `/3_models/` ), this is important to decide what columns from joins to bring in this final data source without exposing whole joined table using `private` statement
- **Benefit:** Creates a "final/to be presented" version of the source.

### LAYER: MODEL (Relationship, Data Modeling, Explore)
**Location:** `/3_models/`
**Purpose:** Defining relationships, joins, that will become "Explores" (Analytical Data Marts); with measures (combined measures) created from different sources (from `/2_extended_sources/`)
- **Rules:** Imports refined files from `/2_extended_sources/`.
- **Actions:** Define `join_one` or `join_many` relationships and complex cross-source measures. Import this into `/2_extended_sources/` to be exposed as soiurce to be used
- **Benefit:** This is the "End Product" for users to run queries against.

### LAYER: ANALYSIS (Output)
**Location:** `/4_analysis/`
**Purpose:** Final consumption layer for queries, reports, and dashboards.
- **Subfolders:**
    - `adhoc/`: Rapid, one-off queries and experimental analysis.
    - `dashboards/`: Production-ready dashboard definitions and visualizations.
    - `use_cases/`: Deep-dive analysis focused on specific business problems.

---

## 3. DIRECTORY STRUCTURE
---

/
├── 0_data/             # Raw table pointers (config.malloy)
├── 1_sources/          # Renaming, PKs, and basic staging
├── 2_extended_sources/ # Curated views & private column handling
├── 3_models/           # Relationship definitions & joins (Explores)
├── 4_analysis/         # Consumption and reporting layer
│   ├── adhoc/          # Quick scratchpad queries
│   ├── dashboards/     # Formal Malloy dashboard files
│   └── use_cases/      # Specialized business analysis
└── README.md           # Project Documentation

---

## 4. DEVELOPMENT WORKFLOW
1. **Define Source:** Add the raw table pointer to `0_sources/config.malloy`.
2. **Extend Source:** Create a file in `1_extended_sources/` that imports the config and uses `source: my_source is base_source extend { ... }`.
3. **Build Model:** Import the extended source into a model file in `3_models/` to define joins and complex metrics.
4. **Analyze:** Create query files in `4_analysis/` importing the models to produce final insights.

---

## 5. TECHNICAL REQUIREMENTS
- **VS Code:** With the official Malloy Extension.
- **Authentication:** Google Cloud SDK / BigQuery Authentication.
- **Connections:** The connection name in `0_sources/config.malloy` must match your local VS Code Malloy connection settings.