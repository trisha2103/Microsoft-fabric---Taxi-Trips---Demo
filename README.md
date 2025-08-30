# 🚕 NYC Yellow Taxi — Microsoft Fabric End-to-End (May–Jul 2025)

> Ingest → Lakehouse → PySpark (Bronze→Silver→Gold) → **Direct Lake** semantic model → **Power BI** report.

<div align="center">
  
**Stack**  
🟢 **Source** • 🔵 **Ingest** • 🟡 **Lakehouse** • 🟣 **Transform** • 🪨 **Model** • 🌸 **Report**

</div>

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

## 🧭 Architecture & Data Flow

```mermaid
flowchart LR
  A[Source\nNYC TLC Open Data\nHTTP Parquet + CSV] --> B[Ingest\nFabric Copy Data\nHTTP -> Lakehouse\nBinary copy ON]
  B --> C[Lakehouse Bronze\n/Files/bronze/yellow/YYYY-MM\n/Files/reference]
  C --> D[Transform\nFabric Notebook (PySpark)\nBronze -> Silver -> Gold]
  D --> E[Gold Tables\nDirect Lake star schema]
  E --> F[Semantic Model\nMeasures + Date table marked]
  F --> G[Power BI Report\nDirect Lake, slicers, KPIs]

  classDef src fill:#2ecc71,color:#0b3d2e,stroke:#0b3d2e,stroke-width:1px;
  classDef ing fill:#4da3ff,color:#0b2b57,stroke:#0b2b57,stroke-width:1px;
  classDef lake fill:#f4c34f,color:#6a4b00,stroke:#6a4b00,stroke-width:1px;
  classDef tfm fill:#b084f5,color:#40286a,stroke:#40286a,stroke-width:1px;
  classDef gold fill:#ffe6a4,color:#6a4b00,stroke:#6a4b00,stroke-width:1px;
  classDef model fill:#27384a,color:#ffffff,stroke:#1b2836,stroke-width:1px;
  classDef rpt fill:#ff99aa,color:#5a0c1f,stroke:#5a0c1f,stroke-width:1px;

  class A src; class B ing; class C lake; class D tfm; class E gold; class F model; class G rpt;
