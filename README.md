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
## Tools & Technologies

### Microsoft Fabric
- **Lakehouse (OneLake storage)**
- **Notebooks (PySpark)** for transforms *(Bronze → Silver → Gold)*
- **Data pipeline (Copy data)** for HTTP → Lakehouse ingest *(binary copy)*
- **Direct Lake semantic model** *(star schema on Gold tables)*

### Power BI
- **Fabric web authoring** with Direct Lake
- **Power BI Desktop** compatible *(PBIX export/import)*
- **DAX** for measures *(e.g., Total Trips, Total Revenue, Tip %)*

### Processing
- **PySpark** for cleansing, type-casting, unioning monthly data, and derived columns

### Data Formats & Access
- **Parquet / CSV** over **HTTP (Anonymous)** sources *(NYC TLC Open Data)*


## Model Details (Measures & Relationships)

### Relationships
- `gold_fact_trips[date_key]` → `gold_dim_date[date_key]` *(active, one-to-many)*
- `gold_fact_trips[do_zone_id]` → `gold_dim_zone[zone_id]` *(active)*
- `gold_fact_trips[pu_zone_id]` → `gold_dim_zone[zone_id]` *(inactive, enable with USERELATIONSHIP in measures)*

---
### Highlights

- ** Direct Lake = import-free, fast, and scalable reporting.

- ** Clean star schema (1 fact + 2 dims) enables flexible time/zone analysis.

- ** Minimal ops: drop new monthly files → re-run notebook → visuals just work.

- ** Transparent pipeline: simple HTTP → Lakehouse Binary copy (no auth).
