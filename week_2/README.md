# Workflow Orchestration

## Data Lake

A data lake is a large and centralized repository that stores raw data in its native format, including structured, semi-structured, and unstructured data. It is designed to support big data processing and analytics by allowing easy and fast access to a large volume and variety of data.

Data lakes are often used in big data environments where traditional data warehouses may not be able to handle the volume or variety of data being collected. They are also used in situations where the purpose of the data is not yet fully understood, and a flexible approach to data storage and analysis is required.

Overall, data lakes provide a powerful platform for storing and analyzing large volumes of data, with the flexibility to handle a wide variety of data types and the ability to support real-time data processing and analytics.

### Data Lake vs Data Warehouse

Handling large volumes of data: Data lakes are designed to handle large volumes of data, including structured, semi-structured, and unstructured data. In contrast, data warehouses are typically optimized for structured data, and may not be able to handle the volume and variety of data that companies need to store and analyze.

Flexibility and agility: Data lakes allow for more flexible and agile processing of data, as they store data in its raw form and allow for schema-on-read. This means that companies can change the way they process and analyze data without having to make significant changes to the underlying data storage structure.

Real-time processing: Data lakes can support real-time processing of data, allowing companies to analyze data in real-time and make decisions based on up-to-date information. In contrast, data warehouses are typically optimized for batch processing and may not be able to handle real-time data.

Cost-effective: Data lakes are generally more cost-effective than data warehouses, as they can be built on top of commodity hardware and open source software. In contrast, data warehouses may require specialized hardware and software licenses, making them more expensive to build and maintain.

Supporting advanced analytics: Data lakes are designed to support advanced analytics, including machine learning, data science, and real-time analytics. These types of analytics may not be possible or practical with a data warehouse.

## ELT vs ETL

Extract, Load and Transform vs. Extract, Transform and Load.

ETL and ELT are two approaches to moving and transforming data from source systems into a target system, such as a data warehouse or data lake.

ETL stands for Extract, Transform, Load, and refers to a process where data is first extracted from source systems, transformed into a structure that is appropriate for the target system, and then loaded into the target system. ETL typically involves using specialized tools to transform and cleanse the data before it is loaded into the target system. ETL is commonly used in data warehousing, where data is structured and processed before being loaded into the warehouse.

ELT, on the other hand, stands for Extract, Load, Transform, and refers to a process where data is first extracted from source systems, loaded into the target system, and then transformed within the target system. In ELT, the transformation is performed within the target system using tools that are already available, such as SQL, without the need for specialized ETL tools. ELT is commonly used in data lakes, where data is stored in its raw format and transformed when it is queried.

The main difference between ETL and ELT is the order in which data is transformed. In ETL, data is transformed before it is loaded into the target system, while in ELT, data is loaded into the target system before it is transformed. Another key difference is the use of specialized ETL tools in ETL, which can be costly and require additional training, while ELT relies on tools that are already available within the target system.

In summary, ETL is commonly used in data warehousing, where data is structured and processed before being loaded into the warehouse, while ELT is commonly used in data lakes, where data is stored in its raw format and transformed when it is queried. ETL involves transforming data before it is loaded into the target system using specialized tools, while ELT involves loading data into the target system and transforming it using tools that are already available.

## Data Swamp

Data lakes can become a data swamp when there is a lack of proper governance, metadata management, data cleansing, data transformation, data storage and retrieval optimization, and data use. These issues can lead to a disorganized, unstructured data environment that is difficult to navigate and analyze. It is important to address these issues to prevent a data lake from becoming a data swamp.

## Data Lake Cloud Providers

* Google Cloud Platform > [Cloud Storage](https://cloud.google.com/storage)
* Amazon Web Services > [Amazon S3](https://aws.amazon.com/s3/)
* Microsoft Azure > [Azure Blob](https://azure.microsoft.com/en-us/services/storage/blobs/)

## Workflow Orchestration

Workflow orchestration is the process of automating and coordinating the execution of tasks or processes within a larger workflow. In the context of data engineering, workflow orchestration involves defining and automating the sequence of data processing tasks required to transform and store data in a way that is useful for analysis.

Workflow orchestration tools allow you to define complex workflows using a graphical interface or code, and automate the execution of those workflows across different systems and environments. These tools can also handle dependencies between tasks, manage data flow, and provide error handling and retry mechanisms.

Overall, workflow orchestration helps to streamline and automate complex processes, making them more efficient, reliable, and scalable. It also provides better visibility and control over the entire workflow, allowing you to monitor performance, identify issues, and make adjustments as needed.

The core features of workflow orchestration typically include:

Task definition: The ability to define individual tasks within a workflow, including their inputs, outputs, and dependencies on other tasks.

Workflow definition: The ability to define the overall workflow structure, including the sequence of tasks and their dependencies.

Execution management: The ability to manage the execution of the workflow, including starting, stopping, and pausing individual tasks or the entire workflow.

Error handling and retry mechanisms: The ability to detect and handle errors that occur during task execution, and to retry failed tasks.

Monitoring and logging: The ability to monitor the progress of the workflow, including task status, task inputs and outputs, and overall workflow performance. Logging is also important for debugging issues that arise during the execution of the workflow.

Integration with external systems: The ability to integrate with external systems, such as data storage platforms, compute platforms, and messaging systems, to allow for the orchestration of complex data engineering workflows.

## Prefect

Prefect is an open-source workflow management system for data engineering. It is designed to make it easy to build, schedule, and monitor data pipelines in Python. Prefect provides a platform-agnostic way to define workflows and execute them across a variety of execution environments, such as local machines, servers, or cloud platforms.

[Introduction to Workflow Orchestration with Prefect- Kevin Kho | SciPy 2022]( https://youtu.be/XL4wgLUp-VA)

We will be exploring a simple Python script that extracts yellow taxi data and loads it into a Postgres database. We will then modify the script to be orchestrated with Prefect, a workflow management system for data engineering.

We can set up our enviroment on GCP or local. First I wanna try it in local

First, I cloned the Data Engineering Zoomcamp week 2 repository.

`git clone https://github.com/discdiver/prefect-zoomcamp.git`

Create a conda environment

`conda create -n zoom python=3.9`

And activate conda

`conda activate zoom`

and then install all requirements using requirements.txt file

`pip install -r requirements.txt`

Open a new terminal in your .yaml file location 

`sudo service docker start`

`docker compose up`

Modify ingest_data.py

```python

if __name__ == '__main__':
    user = "root"
    password = "root"
    host = "localhost"
    port = "5432"
    db = "ny_taxi"
    table_name = "yellow_taxi_trips"
    csv_url = "https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz"

    ingest_data(user, password, host, port, db, table_name, csv_url
```

run `python ingest_data.py`

Finally `Finished ingesting data into the postgres database` now we can go to `localhost:8080` and log into pgadmin. Create a new server: name "Docker localhost", port: "5432", host: "pgdatabase"

OK everything is fine but I wanna setup an environment in GCP. First, start GCP VM instance and copy external IP. After that, I will edit the `config` file in the `.ssh` folder. Replace the old `HostName` address with the new external IP and save the file. And I start remote ssh session from VScode.

I cloned the Data Engineering Zoomcamp week 2 repository.

`git clone https://github.com/discdiver/prefect-zoomcamp.git`

Create a conda environment

`conda create -n zoomcamp python=3.9`

And activate conda

`conda activate zoomcamp`

and then install all requirements using requirements.txt file

`pip install -r requirements.txt`

Modify ingest_data.py in VM

```python
if __name__ == '__main__':
    user = "root"
    password = "root"
    host = "localhost"
    port = "5432"
    db = "ny_taxi"
    table_name = "yellow_taxi_trips"
    csv_url = "https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz"

    ingest_data(user, password, host, port, db, table_name, csv_url
```

Add the required ports in a remote SSH session from VSCode, such as `5432` `8080`

I cloned my Data Engineering Zoomcamp repository because I am using a Docker volume and I want to continue using it this way.

`git clone https://github.com/senatn/data-engineering-course.git`

Open a new terminal in .yaml file location and create a Docker Volume.

`docker volume create --name dtc_postgres_volume_local -d local`

`docker compose up`

run `python ingest_data.py`

To get started, launch pgAdmin and log in. Then, add a new server by pasting the name of the database container as the server name Then, I check the number of rows `SELECT COUNT(1) FROM yellow_taxi_trips1` `1369765`