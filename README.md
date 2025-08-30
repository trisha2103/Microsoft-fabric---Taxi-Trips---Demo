# Microsoft-fabric---Taxi-Trips---Demo

flowchart LR
  subgraph Source["NYC TLC Open Data (HTTP, anonymous)"]
    S1["yellow_tripdata_2025-05.parquet"]
    S2["yellow_tripdata_2025-06.parquet"]
    S3["yellow_tripdata_2025-07.parquet"]
    LKP["taxi_zone_lookup.csv"]
  end

  subgraph FabricPipeline["Fabric Data pipeline (Copy data wizard)"]
    FE["ForEach months\n(2025-05, 2025-06, 2025-07)"]
    CP["Copy activity\n(Binary copy ON)"]
  end

  subgraph Lakehouse["Fabric Lakehouse: hl_taxi"]
    BZ["Files/bronze/yellow/YYYY-MM/"]
    REF["Files/reference/taxi_zone_lookup.csv"]
    SV["Tables (Silver)"]
    GD["Tables (Gold)"]
  end

  subgraph Notebook["Fabric Notebook: bronze_silver_gold.ipynb"]
    BRZ["Bronze → raw files"]
    SIL["Silver → cleaned & unioned trips\n(schema aligned, types fixed)"]
    GOLD["Gold → star model"]
  end

  subgraph Semantic["Direct Lake Semantic Model"]
    DIM_DATE["gold_dim_date"]
    DIM_ZONE["gold_dim_zone"]
    FACT["gold_fact_trips"]
  end

  subgraph BI["Power BI Report (Direct Lake)"]
    RPT["Taxi Trips – May–Jul 2025\nKPIs • Trends • Hourly • Borough"]
  end

  S1 & S2 & S3 --> FE --> CP --> BZ
  LKP --> REF

  BZ --> BRZ --> SIL --> SV
  SV --> GOLD --> GD
  GD --> DIM_DATE & DIM_ZONE & FACT
  DIM_DATE & DIM_ZONE & FACT --> RPT

  %% optional ops
  classDef faint fill:#f7f7f7,stroke:#bbb,color:#444;
  class Source,FabricPipeline,Lakehouse,Notebook,Semantic,BI faint;
