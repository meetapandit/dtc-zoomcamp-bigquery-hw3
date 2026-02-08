# DTC Zoomcamp - Module 3: Data Warehouse (BigQuery) Homework

This project is part of the [DataTalksClub Data Engineering Zoomcamp](https://github.com/DataTalksClub/data-engineering-zoomcamp). It covers BigQuery concepts including external tables, partitioning, clustering, and query optimization using the **2024 NYC Yellow Taxi Trip** dataset.

## Project Structure

```
.
├── upload_data_to_bucket.py   # Downloads Parquet files and uploads them to GCS
├── main.py                    # Project entry point
├── pyproject.toml             # Python project config and dependencies
└── README.md
```

## Setup

### Prerequisites
- Python 3.12+
- Google Cloud SDK authenticated (`gcloud auth application-default login`)
- A GCP project with BigQuery and Cloud Storage APIs enabled

### Install dependencies

```bash
pip install google-cloud-bigquery google-cloud-storage python-dotenv
```

### Upload data to GCS

The `upload_data_to_bucket.py` script downloads the 2024 Yellow Taxi Parquet files (January-June) and uploads them to a GCS bucket.

```bash
python upload_data_to_bucket.py
```

### Create BigQuery external table

```sql
CREATE OR REPLACE EXTERNAL TABLE `your_project.your_dataset.yellow_tripdata_2024_external`
OPTIONS (
  format = 'PARQUET',
  uris = ['gs://your_bucket/yellow_tripdata_2024-*.parquet']
);
```

### Create materialized table

```sql
CREATE OR REPLACE TABLE `your_project.your_dataset.yellow_tripdata_2024_materialized`
AS SELECT * FROM `your_project.your_dataset.yellow_tripdata_2024_external`;
```

## Homework Questions & Answers

Question 1. Counting records
- What is count of records for the 2024 Yellow Taxi Data?
    - 20,332,093

Question 2. Data read estimation
Write a query to count the distinct number of PULocationIDs for the entire dataset on both the tables.

- What is the estimated amount of data that will be read when this query is executed on the External Table and the Table?
    - 0 MB for the External Table and 155.12 MB for the Materialized Table


Question 3. Understanding columnar storage
Write a query to retrieve the PULocationID from the table (not the external table) in BigQuery. Now write a query to retrieve the PULocationID and DOLocationID on the same table.

- Why are the estimated number of Bytes different?
    - BigQuery automatically caches the first queried column, so adding a second column increases processing time but does not affect the estimated bytes scanned.

Question 4. Counting zero fare trips
- How many records have a fare_amount of 0?
    - 8,333

Question 5. Partitioning and clustering
- What is the best strategy to make an optimized table in Big Query if your query will always filter based on tpep_dropoff_datetime and order the results by VendorID (Create a new table with this strategy)
    - Partition by tpep_dropoff_datetime and Cluster on VendorID


Question 6. Partition benefits
- Write a query to retrieve the distinct VendorIDs between tpep_dropoff_datetime 2024-03-01 and 2024-03-15 (inclusive)
    - 310.24 MB for non-partitioned table and 26.84 MB for the partitioned table

Question 7. External table storage
- Where is the data stored in the External Table you created?
    - GCP Bucket

Question 8. Clustering best practices
- It is best practice in Big Query to always cluster your data:
    - False

Question 9. Understanding table scans
- No Points: Write a SELECT count(*) query FROM the materialized table you created. How many bytes does it estimate will be read? Why?
    - It processes 0B as the count of rows is stored in the metadata of teh table created so query engine does not have to access storage and send data over the network.