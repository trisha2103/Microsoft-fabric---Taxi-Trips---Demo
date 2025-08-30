# 🚕 NYC Yellow Taxi — Microsoft Fabric End-to-End (May–Jul 2025)

<p align="center">
> Ingest → Lakehouse → PySpark (Bronze→Silver→Gold) → Direct Lake semantic model → Power BI report.
</p>

<p align="center">
  🟢 <b>Source</b> · 🔵 <b>Ingest</b> · 🟡 <b>Lakehouse</b> · 🟣 <b>Transform</b> · 🪨 <b>Model</b> · 🌸 <b>Report</b>
</p>



---

## 🎯 Problem

City mobility teams need **fresh, trustworthy** insights into taxi demand (trips, revenue, tipping) with the ability to slice by **time** and **geography**—without long refresh times or brittle ETL.

**Constraints**

- Public **HTTP** parquet/CSV files (no credentials)  
- **Interactive** Power BI over **millions of rows**  
- Simple, **repeatable** pipeline that scales month-over-month

---

## ✅ Solution at a Glance

- 🔵 **Ingest** TLC Yellow Taxi Parquet (May–Jul 2025) + **zone lookup CSV** via Fabric **Copy Data** (HTTP → Lakehouse, **Binary copy ON**).
- 🟡 Store raw files in **Bronze** (`/Files/bronze/yellow/YYYY-MM/`) and reference in `/Files/reference/`.
- 🟣 Fabric **Notebook (PySpark)** builds **Silver** (cleaned/typed) and **Gold** (star schema) tables.
- 🪨 A **Direct Lake** semantic model exposes curated tables and DAX measures.
- 🌸 A **Power BI** report delivers KPI cards, trends by date/hour, and borough breakdowns.

---

## 📈 Impact

- **Near-instant interactivity** via **Direct Lake** (no import, no refresh wait).  
- **Trust & clarity** with a clean **star schema** and transparent DAX.  
- **Repeatable**: add the next month, re-run the notebook, the report just works.

---

## 🧰 Tools & Technologies

### Microsoft Fabric
- 🟡 **Lakehouse (OneLake storage)**
- 🟣 **Notebooks (PySpark)** for transforms *(Bronze → Silver → Gold)*
- 🔵 **Data pipeline (Copy data)** for HTTP → Lakehouse ingest *(binary copy)*
- 🪨 **Direct Lake semantic model** *(star schema on Gold tables)*

### Power BI
- 🌐 **Fabric web authoring** with Direct Lake
- 💻 **Power BI Desktop** compatible *(PBIX export/import)*
- ➕ **DAX** for measures *(Total Trips, Total Revenue, Tip %)*

### Processing
- 🐍 **PySpark** for cleansing, type-casting, unioning monthly data, and derived fields

### Data Formats & Access
- 📦 **Parquet/CSV over HTTP (Anonymous)** *(NYC TLC Open Data)*

---

## 🧱 Data Model (ER)

```mermaid
erDiagram
  GOLD_DIM_DATE {
    INT    date_key PK
    DATE   date
    INT    year
    INT    week_of_year
    STRING day_of_week
    INT    is_weekend
  }

  GOLD_DIM_ZONE {
    INT    zone_id PK
    STRING Zone
    STRING Borough
    STRING service_zone
  }

  GOLD_FACT_TRIPS {
    INT    date_key FK
    INT    pu_zone_id
    INT    do_zone_id
    INT    passenger_count
    DOUBLE trip_distance
    DOUBLE fare_amount
    DOUBLE tip_amount
    DOUBLE total_amount
    INT    pickup_hour
    DOUBLE congestion_surcharge
    DOUBLE airport_fee
  }

  GOLD_DIM_DATE ||--o{ GOLD_FACT_TRIPS : "by date_key"
  GOLD_DIM_ZONE ||--o{ GOLD_FACT_TRIPS : "by do_zone_id"
  GOLD_DIM_ZONE ||..o{ GOLD_FACT_TRIPS : "by pu_zone_id (inactive)"

**Relationships**

- `gold_fact_trips[date_key]` → `gold_dim_date[date_key]` *(active, 1 → many)*
- `gold_fact_trips[do_zone_id]` → `gold_dim_zone[zone_id]` *(active)*
- `gold_fact_trips[pu_zone_id]` → `gold_dim_zone[zone_id]` *(inactive; enable via `USERELATIONSHIP` in measures)*

