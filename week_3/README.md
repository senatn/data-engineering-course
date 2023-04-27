# Data Warehouse and BigQuery

## OLAP vs OLTP

LAP and OLTP are two types of systems used in data management, and they have different approaches and purposes.

OLAP (Online Analytical Processing) is a system designed for complex analysis of data. It is used to create multidimensional views of data that can be analyzed from multiple perspectives. OLAP is used for business intelligence, data mining, and decision support, where data is analyzed to find trends, patterns, and insights. OLAP databases are optimized for fast query performance and support complex queries that involve aggregations, roll-ups, and slicing and dicing of data. The data in an OLAP system is updated periodically, and the focus is on historical analysis.

OLTP (Online Transaction Processing), on the other hand, is a system designed for transactional processing. It is used for day-to-day operations that involve processing transactions, such as banking, e-commerce, and inventory management. OLTP databases are optimized for fast data insertion, updates, and deletion, and they ensure data consistency and integrity. The data in an OLTP system is constantly changing, and the focus is on real-time processing.

In summary, OLAP is used for complex analysis and historical reporting, while OLTP is used for day-to-day transaction processing.

|   | OLTP - Online Transaction Processing | OLAP - Online Analytical Processing |
|---|---|---|
| Purpose | Control and run essentail business ops in real-time | Plan, solve problems, support decisions, discover hidden insights  |
| Data Updates  |  Short, fast updates, initiated by user | Data periodically refreshed with scheduled, long-running batch jobs  |
| Database Design | Normalized databases for efficiency  | Denormalized databases for analysis  |
| Space Requirements | Generally small if historical data is archived  | Generally large due to aggegating large datasets  |
| Backup and Recovery | Regular backups required to ensure business continuity and meet legal and governance requirements  | Lost data can be reloaded from OLTP databases as needed in lieu of regular backups  |
| Productivity | Increases productivity of end users  | Increases productivity of business managers, data analysts, and executives  |
| Data view | Lists day-to-day business transactions  | Multi-dimensional view of enterprise data  |
| User examples | Customer-facing personnel, clerks, online shoppers  | Knowledge workers such as data analysts, business analysts, and executives  |
## Data Warehouse

A Data Warehouse is an OLAP (Online Analytical Processing) solution.Is a large, centralized repository that stores data from various sources, typically including transactional databases, external data sources, and other applications. Genarally consist of a raw data, metadata and summary. The data is organized in a way that facilitates querying, analysis, and reporting. 

## BigQuery

BigQuery is a cloud-based data warehousing and analytics service provided by Google Cloud Platform. It is designed to handle large amounts of data and enables users to analyze massive datasets quickly using SQL-like queries. Additionally, it has built-in integration with other Google Cloud services, such as Google Cloud Storage and Google Cloud Dataflow, making it easy to ingest data from various sources. Maximizes flexibility by separating data analysis and storage in different compute engines, thus allowing the customers to budget accordingly and reduce costs.

## Create an External Table

It is possible to generate an external table using a CSV or Parquet file that is located in a Cloud Storage bucket.

```sql
-- Creating external table referring to gcs path
CREATE OR REPLACE EXTERNAL TABLE `exalted-point-376315.dezoomcamp.external_yellow_tripdata`
OPTIONS (
  format = 'parquet',
  uris = ['gs://dtc_data_lake_exalted-point-376315/data/yellow/yellow_tripdata_2021-01.parquet', 'gs://dtc_data_lake_exalted-point-376315/data/yellow/yellow_tripdata_2021-02.parquet']
);
```

```sql
-- Check yello trip data
SELECT * FROM `exalted-point-376315.dezoomcamp.external_yellow_tripdata` LIMIT 10;
```

## Partitioning in BigQuery

Partitioning in BigQuery is a feature that allows you to divide large tables into smaller, more manageable parts based on a specific column or field, known as the partition key. This partitioning can help with query performance and cost optimization. By partitioning a table, you can limit the amount of data that needs to be scanned, which can help to reduce query costs.

When a table is partitioned, it is split into smaller pieces, each containing only the rows that match a specific value or range of values for the partition key. This allows queries to be more focused on a specific subset of the data, reducing the amount of data that needs to be scanned and improving query performance.

We will compare the efficiency of our queries by creating two tables that are partitioned and non partitioned.

```sql
-- Create a non partitioned table from external table
CREATE OR REPLACE TABLE exalted-point-376315.dezoomcamp.yellow_tripdata_non_partitoned AS
SELECT * FROM taxi-rides-ny.nytaxi.external_yellow_tripdata;


-- Create a partitioned table from external table
CREATE OR REPLACE TABLE exalted-point-376315.dezoomcamp.yellow_tripdata_partitoned
PARTITION BY
  DATE(tpep_pickup_datetime) AS
SELECT * FROM exalted-point-376315.dezoomcamp.external_yellow_tripdata;
```

Performing a query on a partitioned table in BigQuery is similar to querying a non-partitioned table, with one major difference being the amount of data that gets processed. As such, We will provide two examples of queries; one for the non-partitioned table, and one for the partitioned table we created earlier, so we can see the difference in data processing.

```sql
SELECT DISTINCT(VendorID)
FROM exalted-point-376315.dezoomcamp.yellow_tripdata_non_partitoned
WHERE DATE(tpep_pickup_datetime) BETWEEN '2021-01-01' AND '2021-01-30';
```

This query will process 40.33 MB when run.

```sql
SELECT DISTINCT(VendorID)
FROM exalted-point-376315.dezoomcamp.yellow_tripdata_partitoned
WHERE DATE(tpep_pickup_datetime) BETWEEN '2021-01-01' AND '2021-01-30';
```

This query will process 19.69 MB when run.

To check the number of rows in each partition of a partitioned table in BigQuery, you can use the following query

```sql
SELECT table_name, partition_id, total_rows
FROM `dezoomcamp.INFORMATION_SCHEMA.PARTITIONS`
WHERE table_name = 'yellow_tripdata_partitoned'
ORDER BY total_rows DESC;
```
## Clustering

Clustering in BigQuery is a feature that allows you to organize data in a table based on the contents of one or more columns. It is similar to partitioning, but instead of dividing the data into separate partitions, it groups related data into clusters based on similarities in the values of the specified columns.

Clustering can help to improve query performance and reduce costs, as it allows BigQuery to skip scanning data that is not relevant to the query. When a query is executed on a clustered table, BigQuery scans only the data that belongs to the relevant clusters, instead of scanning the entire table.

To create a clustered table in BigQuery, you first specify one or more columns that you want to use as the clustering keys. When you load data into the table, BigQuery automatically organizes the data into clusters based on the values of the specified columns.

 In BigQuery, clustering can only be performed on top-level, non-repeated columns. Clustering cannot be performed on nested or repeated fields within a table.

```sql
-- Creating a partition and cluster table
CREATE OR REPLACE TABLE exalted-point-376315.dezoomcamp.yellow_tripdata_partitoned_clustered
PARTITION BY DATE(tpep_pickup_datetime)
CLUSTER BY PULocationID AS
SELECT * FROM exalted-point-376315.dezoomcamp.external_yellow_tripdata;
```

Below are two queries, one for a partitioned table and the other for a partitioned and clustered table, which are identical except for the table names.

```sql
SELECT count(*) as trips
FROM exalted-point-376315.dezoomcamp.yellow_tripdata_partitoned
WHERE DATE(tpep_pickup_datetime) BETWEEN '2021-01-01' AND '2021-01-30' AND PULocationID=43;
```
Approximation: This query will process 20.42 MB when run.
Bytes processed: 20.42 MB

```sql
SELECT count(*) as trips
FROM exalted-point-376315.dezoomcamp.yellow_tripdata_partitoned_clustered
WHERE DATE(tpep_pickup_datetime) BETWEEN '2021-01-01' AND '2021-01-30' AND PULocationID=43;
```
Approximation: This query will process 20.42 MB when run.
Bytes processed: 19.75 MB

