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

## 🧭 Architecture & Data Flow


Here’s a copy-paste block that renders on github.com (uses `<br/>` for line breaks and avoids characters Mermaid dislikes):

```mermaid
flowchart LR
  A[Source<br/>NYC TLC Open Data<br/>HTTP Parquet & CSV] --> B[Ingest<br/>Fabric Copy Data<br/>HTTP → Lakehouse<br/>Binary copy ON]
  B --> C[Lakehouse Bronze<br/>/Files/bronze/yellow/YYYY-MM<br/>/Files/reference]
  C --> D[Transform<br/>Fabric Notebook (PySpark)<br/>Bronze → Silver → Gold]
  D --> E[Gold Tables<br/>Direct Lake star schema]
  E --> F[Semantic Model<br/>Measures + Date table marked]
  F --> G[Power BI Report<br/>Direct Lake, slicers, KPIs]

  classDef src fill:#2ecc71,stroke:#0b3d2e,color:#0b3d2e;
  classDef ing fill:#4da3ff,stroke:#0b2b57,color:#0b2b57;
  classDef lake fill:#f4c34f,stroke:#6a4b00,color:#6a4b00;
  classDef tfm fill:#b084f5,stroke:#40286a,color:#40286a;
  classDef gold fill:#ffe6a4,stroke:#6a4b00,color:#6a4b00;
  classDef model fill:#27384a,stroke:#1b2836,color:#ffffff;
  classDef rpt fill:#ff99aa,stroke:#5a0c1f,color:#5a0c1f;

  class A src; class B ing; class C lake; class D tfm; class E gold; class F model; class G rpt;
