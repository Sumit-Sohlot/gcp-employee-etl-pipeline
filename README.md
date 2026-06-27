# GCP Employee ETL Pipeline

An end-to-end data engineering pipeline built on Google Cloud Platform that ingests, transforms, and loads employee data using Cloud Composer (Airflow), Dataproc (PySpark), and BigQuery.

## Architecture

GCS (Raw Data) → Cloud Composer (Airflow) → Dataproc (PySpark) → GCS (Processed) → BigQuery

## Pipeline Flow

1. **GCS Sensor** – Monitors the raw bucket for the input CSV file
2. **XCom Push/Pull** – Passes file path metadata between Airflow tasks
3. **Dataproc PySpark Job** – Transforms data (uppercases department/city, computes 10% bonus, adds processing timestamp), writes output as Parquet
4. **BigQuery Load** – Loads processed Parquet files into a BigQuery table
5. **BigQuery Validation** – Runs an aggregation query (employee count + avg salary by department) to verify the load

## Tech Stack

| Layer | Technology |
|---|---|
| Orchestration | Apache Airflow (Cloud Composer) |
| Processing | Apache Spark (Dataproc / PySpark) |
| Storage | Google Cloud Storage (GCS) |
| Warehouse | BigQuery |
| Language | Python |
| Cloud | GCP |

## Transformations Applied

- `department` → uppercased
- `city` → uppercased
- `bonus` → computed as 10% of salary
- `processing_timestamp` → added at runtime

## Files

- `dags/gcp_employee_pipeline_dag.py` – Airflow DAG definition
- `spark/employee_etl.py` – PySpark transformation script
- `data/employee_data.csv` – Sample input dataset

## Setup

1. Upload `employee_data.csv` to `gs://<your-raw-bucket>/input/`
2. Upload `employee_etl.py` to `gs://<your-raw-bucket>/scripts/`
3. Deploy the DAG to your Cloud Composer environment's `dags/` folder
4. Trigger the DAG manually from the Airflow UI

## Key Concepts Demonstrated

- Airflow DAG design with task dependencies
- GCS file sensing before pipeline execution
- XCom for inter-task communication
- Dataproc job submission from Airflow
- Parquet as columnar storage format
- BigQuery table loading and SQL validation
