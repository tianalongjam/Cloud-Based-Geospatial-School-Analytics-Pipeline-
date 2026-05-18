# Cloud-Based Geospatial School Analytics Pipeline

End-to-end cloud data engineering pipeline built using **Google Cloud Platform (GCP)** to process, transform, and analyze Wisconsin school datasets with **GCS, Dataform, BigQuery, and geospatial SQL**.

This project demonstrates cloud infrastructure setup, ETL pipeline development, geospatial querying, and large-scale analytics using Google Cloud services.

---

## Project Goal

Build a scalable cloud pipeline that:

- Stores raw education datasets in **Google Cloud Storage (GCS)**
- Automates transformations with **Dataform**
- Loads processed data into **BigQuery**
- Performs geospatial analysis on Wisconsin schools and counties
- Answers analytical questions using SQL and BigQuery

---

## Features

### Cloud Infrastructure
- Provisioned and configured a **GCP virtual machine**
- Connected via SSH with secure authentication
- Set up cloud development environment with JupyterLab

### Data Storage & ETL
- Uploaded Parquet datasets to **Google Cloud Storage**
- Built **Dataform pipelines** for automated transformations
- Created dependency-managed workflows between datasets

### BigQuery Analytics
Performed SQL analysis to answer questions such as:

- Number of Wisconsin counties
- Public school counts
- County-level high school distribution
- Nearest public high schools to middle schools
- BigQuery free-tier cost estimation

### Geospatial Analytics
Used BigQuery geographic functions:

- `ST_GEOGPOINT`
- Spatial joins
- Distance calculations
- County-school mapping

---

## Technologies Used

- Python
- Google Cloud Platform (GCP)
- Google Cloud Storage (GCS)
- BigQuery
- Dataform
- SQL / BigQuery Pipe Syntax
- PyArrow
- Pandas
- Jupyter Notebook

---

## Project Structure

```bash
.
├── src/
│   ├── definitions/
│   │   ├── wi_counties.sqlx
│   │   ├── schools.sqlx
│   │   └── wi_county_schools.sqlx
│   └── p8.ipynb
├── README.md
├── requirements-dev.txt
├── ai.md
└── .gitignore
```

---

## Data Pipeline Workflow

1. Upload raw Wisconsin school dataset → GCS bucket
2. Load dataset into BigQuery through Dataform
3. Create Wisconsin county table
4. Perform geospatial joins between schools and counties
5. Execute analytical queries in BigQuery
6. Generate insights on school distribution and geography

---

## Example Analyses

- Count public schools across Wisconsin
- Identify counties with multiple cities containing ≥3 public high schools
- Compute nearest high school for every public middle school in Dane County
- Estimate BigQuery usage limits under free-tier constraints

---

## Key Learning Outcomes

This project strengthened experience with:

✔ Cloud infrastructure deployment (VMs, SSH)  
✔ ETL pipelines in GCP  
✔ Dataform workflow automation  
✔ Geospatial SQL analysis  
✔ BigQuery optimization and querying  
✔ Cloud authentication and security practices  

---

## Author

**Tiana Longjam**  
Computer Science & Data Science Student — University of Wisconsin–Madison