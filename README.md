# Cloud-Based Geospatial School Analytics Pipeline

An end-to-end cloud data pipeline built on **Google Cloud Platform** that ingests raw Wisconsin school data, transforms it with **Dataform**, and joins it against public geographic boundary data in **BigQuery** to answer geospatial analytics questions — all orchestrated from a **Jupyter notebook** running on a GCP VM.

---

## Overview

Raw school records (name, type, coordinates) arrive as a single Parquet file with no relationship to geographic boundaries. This pipeline turns that flat file into a queryable, location-aware dataset by:

1. Landing the raw file in **Google Cloud Storage**
2. Loading it into **BigQuery** and joining it against public county boundary polygons
3. Producing a single analytics-ready table where every school is tagged with the county it physically sits in
4. Answering real analytical questions against that table — distribution counts, geographic clustering, and nearest-neighbor distance calculations — using SQL, including BigQuery's pipe syntax and GIS functions

## Architecture

```
 wi-schools-raw.parquet (local file)
            │
            ▼
   Google Cloud Storage (GCS bucket)
            │
            ▼
   Dataform pipeline (SQL, dependency-managed)
   ┌─────────────────────┬─────────────────────┐
   │      schools          │      wi_counties      │
   │  LOAD DATA OVERWRITE  │  bigquery-public-data  │
   │  from GCS parquet     │  filtered to WI (FIPS 55)│
   └──────────┬───────────┴───────────┬─────────┘
              │                       │
              └──────────┬────────────┘
                          ▼
              wi_county_schools
     (spatial join: ST_CONTAINS + ST_GEOGPOINT)
                          │
                          ▼
              BigQuery analytics queries
```

## Data Pipeline

Three Dataform actions (`src/definitions/*.sqlx`) define the transformation graph:

| File | Type | Purpose |
|---|---|---|
| `schools.sqlx` | `operations` | Loads the raw schools Parquet file from GCS directly into a BigQuery table via `LOAD DATA OVERWRITE`. Marked `hasOutput: true` since Dataform can't infer schema from a non-`SELECT` statement. |
| `wi_counties.sqlx` | `table` | Pulls county boundary polygons from the public `bigquery-public-data.geo_us_boundaries.counties` dataset, filtered to Wisconsin (FIPS `55`). |
| `wi_county_schools.sqlx` | `table` | Spatially joins schools to counties: converts each school's lat/long into a geography point (`ST_GEOGPOINT`) and tests which county polygon contains it (`ST_CONTAINS`). Uses Dataform's `${ref(...)}` syntax so dependency order is inferred automatically rather than hardcoded. |

Dataform compiles these into a dependency graph (see `compiled-graph.png`) and executes them in the correct order — `schools` and `wi_counties` first, `wi_county_schools` last.

## Analysis Performed

Once `wi_county_schools` exists, SQL is used to answer:

- How many counties exist in Wisconsin
- How many public schools exist statewide
- Which counties have at least two cities each containing three or more public high schools (multi-stage aggregation using BigQuery pipe syntax)
- How many times a given query could run before exhausting BigQuery's free-tier data-processing allowance
- For every public middle school in a given county, the closest public high school by geographic distance (`ST_DISTANCE` + `MIN_BY`)

## Technologies Used

- **Compute:** GCP Compute Engine VM (Ubuntu), accessed via SSH
- **Storage:** Google Cloud Storage
- **Transformation:** Dataform (SQL-based, dependency-aware pipelines)
- **Warehouse / Analytics:** BigQuery, including geospatial functions and pipe syntax
- **Notebook / Orchestration:** JupyterLab, Python (`google-cloud-bigquery`, `google-cloud-dataform`, `pyarrow`, `pandas`)

## Project Structure

```
.
├── README.md
├── gitignore
├── compiled-graph.png              # Dataform's compiled dependency graph
└── src/
    ├── p.ipynb                     # drives the pipeline end-to-end and runs all analysis
    └── definitions/
        ├── schools.sqlx
        ├── wi_counties.sqlx
        └── wi_county_schools.sqlx
```

## Author

**Tiana Longjam**
Computer Science & Data Science Student — University of Wisconsin–Madison
