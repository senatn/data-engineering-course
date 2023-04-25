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

A Data Warehouse is a large, centralized repository that stores data from various sources, typically including transactional databases, external data sources, and other applications. The data is organized in a way that facilitates querying, analysis, and reporting.

## BigQuery

BigQuery is a cloud-based data warehousing and analytics service provided by Google Cloud Platform. It is designed to handle large amounts of data and enables users to analyze massive datasets quickly using SQL-like queries. BigQuery allows users to store and query data using a pay-as-you-go model, meaning that they only pay for the amount of data they process and store. Additionally, it has built-in integration with other Google Cloud services, such as Google Cloud Storage and Google Cloud Dataflow, making it easy to ingest data from various sources.
