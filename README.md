# NYC Yellow Taxi — Microsoft Fabric End-to-End (May–Jul 2025)

![Report overview](Taxi%20Trips_Report.png)

> An end-to-end analytics project built on **Microsoft Fabric**: ingest → lakehouse → PySpark transform (Bronze→Silver→Gold) → **Direct Lake** semantic model → Power BI report.

---

## Table of contents
- [Problem](#problem)  
- [Solution at a glance](#solution-at-a-glance)  
- [Impact](#impact)  
- [Architecture & Data Flow](#architecture--data-flow)  
- [Gold ER Diagram](#gold-er-diagram)  
- [Tools & Technologies](#tools--technologies)  
- [What’s in this repo](#whats-in-this-repo)  
- [Repro in Fabric (quick start)](#repro-in-fabric-quick-start)  
- [Model details (measures & relationships)](#model-details-measures--relationships)  
- [Next steps / ideas](#next-steps--ideas)  
- [License](#license)

---

## Problem
City mobility teams and operations analysts need **fresh, trustworthy** insight into taxi demand (trips, revenue, tipping behavior) with the ability to **slice by time and geography**—without waiting on long refreshes or brittle ETL chains.

**Constraints**
- Sources are public, **HTTP-hosted parquet/CSV** files (no credentials).
- Analysts want **interactive Power BI** with **low latency** on millions of rows.
- A single pipeline should be maintainable and extensible (new months, new features).

---

## Solution at a glance
- **Ingest** NYC Yellow Taxi parquet files (May–Jul 2025) and the **zone lookup** CSV via Fabric **Data pipeline (HTTP → Lakehouse)** with **Binary copy ON**.  
- Store raw files in Lakehouse **Bronze** (`/Files/bronze/yellow/YYYY-MM/`), reference data in `/Files/reference/`.
- A **PySpark Notebook** builds **Silver** (cleaned/typed) and **Gold** (star schema) tables.
- A **Direct Lake** semantic model exposes curated tables to **Power BI** with DAX measures.
- A **Power BI** report delivers KPI cards, trends by date/hour, and borough breakdowns.

---

## Impact
- **Interactive performance**: Direct Lake reads directly from the Lakehouse without import, enabling near-instant report interaction at scale.  
- **Trustworthy**: Single, governed star schema (date and zone dimensions) + clear DAX.  
- **Repeatable**: Add new months to the pipeline list (or schedule monthly) → Silver/Gold refresh → report auto-updates.

---
## Architecture

flowchart LR
    A["Source\nNYC Taxi Open Data (HTTP)\n- yellow_tripdata_2025-05.parquet\n- yellow_tripdata_2025-06.parquet\n- yellow_tripdata_2025-07.parquet\n- taxi_zone_lookup.csv"]:::src
    B["Ingest\nFabric Data Pipeline (HTTP to Lakehouse)\n- Binary copy ON\n- Paths: /Files/bronze/yellow/YYYY-MM/ and /Files/reference/"]:::ing
    C["Lakehouse (Bronze files)\nRaw parquet folders in OneLake"]:::lake
    D["Transform (PySpark)\nBronze -> Silver -> Gold\n- type casting and union\n- add missing airport_fee\n- write star schema"]:::xform
    E["Semantic Model (Direct Lake)\n- gold_dim_date\n- gold_dim_zone\n- gold_fact_trips\n- DAX measures"]:::model
    F["Power BI Report\nKPI, trends, slicers"]:::report

    A --> B --> C --> D --> E --> F

    classDef src fill:#2ecc71,stroke:#1f8f59,color:#0b3d2e,stroke-width:1.5px;
    classDef ing fill:#4da3ff,stroke:#1b5fb3,color:#0b2b57,stroke-width:1.5px;
    classDef lake fill:#f4c542,stroke:#b38700,color:#4d3a00,stroke-width:1.5px;
    classDef xform fill:#b07ce6,stroke:#723ba8,color:#2e1450,stroke-width:1.5px;
    classDef model fill:#2d3e50,stroke:#1b2633,color:#e9eef4,stroke-width:1.5px;
    classDef report fill:#f28aa6,stroke:#c94c6b,color:#4a0e1b,stroke-width:1.5px;
