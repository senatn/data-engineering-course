# Workflow Orchestration

## Data Lake

A data lake is a large and centralized repository that stores raw data in its native format, including structured, semi-structured, and unstructured data. It is designed to support big data processing and analytics by allowing easy and fast access to a large volume and variety of data.

Data lakes are often used in big data environments where traditional data warehouses may not be able to handle the volume or variety of data being collected. They are also used in situations where the purpose of the data is not yet fully understood, and a flexible approach to data storage and analysis is required.

Overall, data lakes provide a powerful platform for storing and analyzing large volumes of data, with the flexibility to handle a wide variety of data types and the ability to support real-time data processing and analytics.

## Data Lake vs Data Warehouse

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

# Introduction to Workflow Orchestration

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

# Introduction to Prefect concepts

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

Add the required ports in a remote SSH session from VSCode, such as `5432` `8080` By doing this, we will be able to access these ports in the local browser via a remote connection.

I cloned my Data Engineering Zoomcamp repository because I am using a Docker volume and I want to continue using it this way.

`git clone https://github.com/senatn/data-engineering-course.git`

Open a new terminal in .yaml file location and create a Docker Volume.

`docker volume create --name dtc_postgres_volume_local -d local`

`docker-compose up`

`python ingest_data.py`

To get started, launch pgAdmin and log in. Then, add a new server by pasting the name of the database container as the server name Then, I check the number of rows `SELECT COUNT(1) FROM yellow_taxi_trips` `1369765`

Okay, by checking in pgAdmin4, we can confirm that the data has been successfully ingested into the PostgreSQL database. However, the process of executing the Python script was done manually. By utilizing a workflow orchestration tool, I will be able to automate this process and schedule it to run automatically. This way, I won't have to manually trigger the script anymore.

Moreover, using a workflow orchestration tool offers additional functionalities, such as enhanced visibility into the workflow, the ability to add resilience to the dataflow with automatic retries or caching, and more.
## Prefect

Prefect is an open-source workflow management system for data engineering. It is designed to make it easy to build, schedule, and monitor data pipelines in Python. Prefect provides a platform-agnostic way to define workflows and execute them across a variety of execution environments, such as local machines, servers, or cloud platforms.

[Introduction to Workflow Orchestration with Prefect- Kevin Kho | SciPy 2022]( https://youtu.be/XL4wgLUp-VA)

Some concepts in Prefect are:

Task: A unit of work in Prefect. Tasks are defined as Python functions and can be connected to other tasks to form a workflow.

Flow: A collection of tasks that are connected to form a workflow. Flows can be executed and monitored by Prefect.

Edge: A connection between two tasks in a flow. Edges define the data dependencies between tasks.

Parameter: A value that is passed to a task at runtime. Parameters can be used to make tasks more flexible.

Parameterized flow: A flow that has one or more parameters. Parameterized flows can be executed multiple times with different parameter values.

State: The current status of a task or flow. States can be used to track the progress of a workflow and to handle errors and retries.

Task Runner: A component in Prefect that executes tasks. Task runners are responsible for handling task dependencies, retries, and state management.

Flow Runner: A component in Prefect that executes flows. Flow runners are responsible for handling flow scheduling, state management, and monitoring.

## Python Decorators

In Python, a decorator is a special kind of function that can modify or enhance the behavior of another function or class. Decorators allow you to add functionality to existing functions or classes without modifying their source code.

Decorators are defined using the "@" symbol followed by the decorator function name, which is applied to the target function or class. The decorator function takes the target function or class as an argument and returns a modified version of it.

## Loading data into Postgres using Prefect

Let's transform `ingest_data.py` into a Prefect flow. By utilizing the task and flow concept, we can decompose the ingest_data Python script into several tasks and flows, resulting in a more comprehensive visualization of our workflow. Essentially, we will divide the current ingest_data.py into multiple functions and integrate Python decorators to construct a Prefect workflow.

```python
#!/usr/bin/env python
# coding: utf-8
import os
import argparse
from time import time
import pandas as pd
from sqlalchemy import create_engine
from prefect import flow, task # IMPORT PREFECT

# FLOWS CONTAINS TASK SO TRANSFORM ingest_data() INTO A TASK BY ADDING @task DECORATOR
@task(log_prints=True, retries=3)
def ingest_data(user, password, host, port, db, table_name, url):
    
    # the backup files are gzipped, and it's important to keep the correct extension
    # for pandas to be able to open the file
    if url.endswith('.csv.gz'):
        csv_name = 'yellow_tripdata_2021-01.csv.gz'
    else:
        csv_name = 'output.csv'

    os.system(f"wget {url} -O {csv_name}")
    postgres_url = f'postgresql://{user}:{password}@{host}:{port}/{db}'
    engine = create_engine(postgres_url)

    df_iter = pd.read_csv(csv_name, iterator=True, chunksize=100000)

    df = next(df_iter)

    df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime)
    df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)

    df.head(n=0).to_sql(name=table_name, con=engine, if_exists='replace')

    df.to_sql(name=table_name, con=engine, if_exists='append')

# CREATE A main_flow FUNCTIPN AND ADD @flow DECORATOR ABOVE FUNCTION     
@flow(name="Ingest Flow")
def main_flow():
    user = "root"
    password = "root"
    host = "localhost"
    port = "5432"
    db = "ny_taxi"
    table_name = "yellow_taxi_trips"
    csv_url = "https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz"

    ingest_data(user, password, host, port, db, table_name, csv_url)

if __name__ == '__main__':
    main_flow()
```

First clean database so go back to pgadmin `DROP TABLE yellow_taxi_trips` Now let’s run the `ingest_data.py` as a Prefect flow `python ingest_data.py`

Great, so the script has been executed as a flow. Now, we can simplify the script by transforming it into an extract and transform process before loading the data into the PostgreSQL database.

To begin, let's break down the large `ingest_data` function into smaller functions, which will allow us to gain more visibility into the tasks that are running and identify potential causes of failures.

We'll start by creating a new task called `extract_data`, which will receive the CSV URL as input and return the results. Given that this task pulls data from an external system that I may not control, I want to include automatic retries and caching, so that the task doesn't need to be re-run if it has already been executed.

```python
from prefect.tasks import task_input_hash
from datetime import timedelta

@task(log_prints=True, tags=["extract"], cache_key_fn=task_input_hash, cache_expiration=timedelta(days=1))
def extract_data(url: str):
    # the backup files are gzipped, and it's important to keep the correct extension
    # for pandas to be able to open the file
    if url.endswith('.csv.gz'):
        csv_name = 'yellow_tripdata_2021-01.csv.gz'
    else:
        csv_name = 'output.csv'
    
    os.system(f"wget {url} -O {csv_name}")

    df_iter = pd.read_csv(csv_name, iterator=True, chunksize=100000)

    df = next(df_iter)

    df.tpep_pickup_datetime = pd.to_datetime(df.tpep_pickup_datetime)
    df.tpep_dropoff_datetime = pd.to_datetime(df.tpep_dropoff_datetime)

    return df
```

`log_prints=True:` This parameter enables logging for the task, which means that any print statements in the task function will be logged by Prefect.

`tags=["extract"]:` This parameter allows you to add tags to the task. Tags are used to categorize and organize tasks within a flow.

`retries=3:` This parameter specifies the number of times the task should be retried if it fails due to an exception or error. In this case, the task will be retried up to 3 times.

`cache_key_fn=task_input_hash:` This parameter specifies the function to use for generating the cache key for the task. In this case, `task_input_hash` is used, which calculates a unique hash value for the input parameters of the task. This helps Prefect to cache the task's output based on its input parameters.

`cache_expiration=timedelta(days=1):` This parameter specifies the expiration time for the task's cache. In this case, the cache will expire after 1 day, meaning that if the task is executed again after 1 day, its output will be recalculated even if its input parameters haven't changed.

Moving on, upon examining the data in `ny_taxi`, you may notice that the passenger count in row 4 is 0. Therefore, we need to perform a data cleansing transformation step before loading the data into PostgreSQL. To accomplish this, I will create a new task called `transform_data`. It's worth noting that the dataframe can be effortlessly passed to the following task.

```python
@task(log_prints=True)
def transform_data(df):
    print(f"pre: missing passenger count: {df['passenger_count'].isin([0]).sum()}")
    df = df[df['passenger_count'] != 0]
    print(f"post: missing passenger count: {df['passenger_count'].isin([0]).sum()}")
    return df
```
`isin()` and `sum()` are both functions provided by the pandas library in Python for working with dataframes.

The `isin()` function allows you to check whether a particular element or value is present in a dataframe column or not. It takes a list, array, or series of values as input and returns a boolean dataframe where True indicates that the element is present in the corresponding row, and False indicates that it is not.

The `sum()` function, on the other hand, calculates the sum of values in a dataframe column or row. By default, it operates on the columns of the dataframe, but you can specify the axis parameter to operate on rows instead.

`print({df['passenger_count'].isin([0]).sum()}"))` prints the total sum of `passenger_count` values that do not exist.

Lastly, let’s actually simplify the original ingest_data() function and rename this to load_data()

```python
@task(log_prints=True, retries=3)
def load_data(user, password, host, port, db, table_name, df):
  postgres_url = f'postgresql://{user}:{password}@{host}:{port}/{db}'
  engine = create_engine(postgresql_url)

  df.head(n=0).to_sql(name=table_name, con=engine, if_exists='replace')
  df.to_sql(name=table)name, con=engine, if_exists='append')
```

main() function now looks like:

```python
@flow(name="Ingest Flow")
def main(user, password, host, port, db, table_name, csv_url):
  raw_data = extract_data(csv_url)
  data = transform_data(raw_data)
  load_data(user, password, host, port, db, table_name, data)
Let’s clean the db and run the flow again with drop table yellow_taxi_trips
```

Now let's run our flow `python ingest_data.py`

And if we look at the DB, we can see there are no more passenger counts of 0.

We can enhance our flow further by incorporating more of Prefect's features. For example, we could introduce parameterization to the flow, allowing us to specify a table name as an input. This would make it easy to switch between different tables each time the flow is executed.

```python
@flow(name="Ingest Data")
def main(table_name: str):
  user = "postgres"
  password = "admin"
  host = "localhost"
  port = "5433"
  db = "ny_taxi"
  table_name = "yellow_taxi_trips"
  csv_url = "https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz"

  raw_data = extract_data(csv_url)
  data = transform_data(raw_data)
  load_data(user, password, host, port, db, table_name, data)

if __name__ == "__main__":
  main("yellow_taxi_trips")
```
In Prefect, a subflow is a way to encapsulate a group of tasks within a larger flow. It allows you to break down a complex workflow into smaller, more manageable sub-workflows, each with its own set of tasks. 

Using subflows can make your workflows more modular and easier to maintain. For example, you might have a subflow that loads data from an external source, another subflow that cleans and transforms the data, and a third subflow that loads the data into a database. Each of these subflows could be developed and tested separately, then combined together into a larger, more complex workflow.

One benefit of using subflows is that they can be reused across multiple workflows. For example, if you have a subflow that handles authentication and authorization, you can reuse that subflow in multiple workflows that require authentication and authorization.

Another benefit of using subflows is that they can help to improve the readability and organization of your code. By breaking down a large, complex workflow into smaller, more focused subflows, you can make it easier to understand and maintain the workflow over time.

Add function log_subflow():

```python
@flow(name="Subflow", log_prints=True)
def log_subflow(table_name : str):
  print(f"Logging Subflow for {table_name}")
```

Add newline log_subflow(table_name) to the main() flow

## Prefect Orion UI

Prefect Orion UI is a user interface for Prefect 2.0. It allows you to gain complete visibility and control over your workflows.

To configure the Prefect API URL in the Prefect configuration, you need to run the following command:

`prefect config set PREFECT_API_URL=http://127.0.0.1:4200/api`

The Prefect API URL is the endpoint that Prefect uses to communicate with the Prefect API, which is a REST API providing programmatic access to Prefect flows, tasks, and runs.

Setting the Prefect API URL to `http://127.0.0.1:4200/api` means that Prefect will use a local instance of the Prefect API running at that URL. This is usually done for local development or testing purposes, where you want to run the Prefect API on your local machine.

`prefect orion start`

Upon opening localhost, the Prefect UI can be seen, which provides a user-friendly dashboard to view the history of all flow runs.

If we execute a flow again, we can see the same flow name in our terminal and in the Prefect UI. The UI provides a convenient dashboard where we can view the run history of all our flows.

The UI also has features such as Deployments, Workqueues, Blocks, Notifications, and Tas kRun Concurrency. Tas kRun Concurrency can be configured for tasks by adding a tag to the task and setting a limit through a CLI command. Notifications are essential for keeping us informed when something goes wrong with our system. Instead of monitoring the dashboard frequently, we can set up notifications to alert us when an issue occurs that requires investigation.

In Prefect, blocks are a basic component that stores configurations and provides an interface for interacting with external systems. There are various types of blocks that you can create, and you can even develop your own. Block names are unchangeable, which means they can be reused across multiple flows. You can also build upon existing blocks or install them as part of the Integration Collection, which contains pre-built tasks and blocks that can be installed via pip. For instance, many users use the SqlAlchemy block.

Now we will take our postgres configuration and store that in a block. We have already installed `prefect-sqlalchemy` via `requirements.txt` Create a SQL Alchemy Connector block and place all configuration in there. Use the `SyncDriver` called `postgresql + psycopg2`, give a name for block and use the arguments of our database. 

```python
user = "root"
password = "root"
host = "localhost"
port = "5432"
db = "ny_taxi"
```

After submitting your information change the flow code and add `from prefect_sqlalchemy import SqlAlchemyConnector`

change load_data() function:

```python
@task(log_prints=True, retries=3)
def load_data(table_name, df):

  connection_block = SqlAlchemyConnector.load("postgres-connector")
  with connection_block.get_connection(begin=False) as engine:
      df.head(n=0).to_sql(name=table_name, con=engine, if_exists='replace')
      df.to_sql(name=table_name, con=engine, if_exists='append')
```

It loads a Prefect block called `SqlAlchemyConnector` with the ID "postgres-connector". This block represents a connection to a PostgreSQL database using the SQLAlchemy library.

It uses this block to create a connection to the database, and then uses the connection to append the data in the `df` DataFrame to a table in the database with the name specified by the `table_name` argument. If the table does not exist, it is created. If it does exist, the data is appended to the existing table.

let's run our flow `python ingest_data_flow.py`

# ETL with GCP & Prefect

We will prepare the environment first. If you have stopped and restarted a GCP VM instance, make sure the IP in your `config` file is up to date. Open remote VSCode and activate the Zoomcamp Conda environment. In another terminal, use prefect orion start to start the Prefect UI on your local machine. Create a new folder called `02_gcp`, and in that folder, create a file named `elt_web_to_gcs.py`. 

The main flow of the program consists of several task functions that are called by a single function. The purpose of this function is to retrieve yellow taxi data from a web source, perform data cleaning and transformation operations, and store the resulting data as a parquet file in our data lake located in Google Cloud Storage (GCS).

Parquet is an open-source columnar storage format that is optimized for use with large-scale data processing frameworks like Hadoop and Spark. It provides a highly compressed and efficient way of storing data in a columnar fashion, which can lead to significant improvements in query performance, especially when dealing with large datasets. Parquet files can be used in a variety of applications, such as data warehousing, big data analytics, and machine learning. They can be read and written using a variety of programming languages, including Python, Java, and Scala.

```python
from pathlib import Path
import pandas as pd
from prefect import flow, task
from prefect_gcp.cloud_storage import GcsBucket


@task(retries=3)
def fetch(dataset_url: str) -> pd.DataFrame:
    """Read taxi data from web into pandas DataFrame"""
    df = pd.read_csv(dataset_url)
    return df


@task(log_prints=True)
def clean(df: pd.DataFrame) -> pd.DataFrame:
    """Fix dtype issues"""
    df["tpep_pickup_datetime"] = pd.to_datetime(df["tpep_pickup_datetime"])
    df["tpep_dropoff_datetime"] = pd.to_datetime(df["tpep_dropoff_datetime"])
    print(df.head(2))
    print(f"columns: {df.dtypes}")
    print(f"rows: {len(df)}")
    return df


@task()
def write_local(df: pd.DataFrame, color: str, dataset_file: str) -> Path:
    """Write DataFrame out locally as parquet file"""
    path = Path(f"data/{color}/{dataset_file}.parquet")
    df.to_parquet(path, compression="gzip")
    return path


@task()
def write_gcs(path: Path) -> None:
    """Upload local parquet file to GCS"""
    gcs_block = GcsBucket.load("zoom-gcs")
    gcs_block.upload_from_path(from_path=path, to_path=path)
    return


@flow()
def etl_web_to_gcs() -> None:
    """The main ETL function"""
    color = "yellow"
    year = 2021
    month = 1
    dataset_file = f"{color}_tripdata_{year}-{month:02}"
    dataset_url = f"https://github.com/DataTalksClub/nyc-tlc-data/releases/download/{color}/{dataset_file}.csv.gz"

    df = fetch(dataset_url)
    df_clean = clean(df)
    path = write_local(df_clean, color, dataset_file)
    write_gcs(path)


if __name__ == "__main__":
    etl_web_to_gcs()
```

`pathlib`: A module for object-oriented file system paths. It offers an object-oriented way of handling file system paths instead of using string-based paths directly. Path is a class from this module that represents a file system path.

`prefect_gcp`: A set of Prefect integrations for interacting with Google Cloud Platform (GCP) services. `cloudstorage` is a subpackage of `prefect_gcp` that provides tasks and utilities for interacting with GCP Cloud Storage. `GcsBucket`: A class from `prefect_gcp.cloudstorage` that represents a GCP Cloud Storage bucket. It provides methods for interacting with the bucket, such as uploading and downloading files.

The code defines four tasks using the `@task` decorator: `fetch`, `clean`, `write_local`, and `write_gcs`. Each task takes input and produces output as defined by its function signature. The `fetch` task reads a CSV file from a given URL and returns a pandas DataFrame. The `clean` task performs data cleaning operations on the input DataFrame, such as converting columns to the appropriate data type and printing summary information. The `write_local` task writes the cleaned DataFrame to a parquet file on the local file system and returns the file path. The `write_gcs` task uploads the parquet file to a Google Cloud Storage bucket using the GcsBucket block from the `prefect_gcp.cloud_storage` module. The `etl_web_to_gcs` function defines the main flow of the program using the `@flow` decorator. It defines the workflow by chaining the tasks together and passing the outputs of one task as the inputs to the next task. It also defines the input parameters for the workflow, such as the year and month of the dataset to fetch. Finally, the code executes the `etl_web_to_gcs` workflow if the script is run as the main program.

ETL (Extract, Transform, Load) process to retrieve data from a web source, clean and transform the data, and store it as a parquet file in Google Cloud Storage.

## Prefect Blocks: GCS Bucket

In SSH terminal use `prefect block register -m prefect_gcp` to add additional GCP blocks.

`prefect block register` is a command in the Prefect CLI that is used to register a block with the Prefect Blocks registry. `-m` is a flag that specifies the block module to register. In this case, `prefect_gcp` is the block module being registered. So, `prefect block register -m prefect_gcp` would register the `prefect_gcp` module as a block with the Prefect Blocks registry. This would allow other users to discover and use the `prefect_gcp` block in their workflows.

```
Successfully registered 6 blocks

┏━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ Registered Blocks         ┃
┡━━━━━━━━━━━━━━━━━━━━━━━━━━━┩
│ BigQuery Warehouse        │
│ GCP Cloud Run Job         │
│ GCP Credentials           │
│ GcpSecret                 │
│ GCS Bucket                │
│ Vertex AI Custom Training │
│ Job                       │
└───────────────────────────┘

To configure the newly registered blocks, go to the Blocks page in the Prefect UI: http://127.0.0.1:4200/blocks/catalog
```
Go to the Prefect Orion UI in your browser and click on "Blocks". Click "Add a Block" and select "GCS Bucket". Choose a name for your block (e.g. "zoom-gcs"), add the name of your GCS bucket (In GCP under Buckets). Under GCP Credentials, click ADD and choose a name for your GCS Credential block name (e.g. "zoom-gcp-creds")

## Prefect Blocks: GCS Credentials and Service Accounts

GCS (Google Cloud Storage) Credentials refer to the authentication credentials that allow you to access your Google Cloud Storage resources, such as buckets and objects. These credentials typically include a Service Account, which is a special type of Google account that belongs to your application or a virtual machine (VM) instance rather than to an individual end user.

Service Accounts can be used to authenticate to various Google Cloud services, including Google Cloud Storage, and are used to manage access to resources in the Google Cloud Platform (GCP) environment. A Service Account is associated with a set of credentials that are used to authenticate your application with the Google Cloud APIs.

To use GCP and GCS, you will typically need to create a Service Account and provide it with the appropriate permissions to access your resources. Once you have created your Service Account, you can use the associated credentials to authenticate your application when accessing Google Cloud Storage.

Before click ADD button under the GCP Credentials In Prefect Orion UI go to the GCP - IAM & Admin - Service Accounts page. Click "+ Create Service Account" choose a name and add BigQuery Admin and Storage Admin.

To create a new key for the service account, select Manage Keys from the Service Account page in the GCP console. Then, click on Add Key and choose Create New Key. Select JSON as the key type and save the file in a secure location. Open the saved file with a text editor and copy the contents. Then, paste the copied text into the Service Account Info field on the credential Block creation page in Orion. Click on Create, and you will be taken back to the GCS Bucket Block creation page. Select the credential you just created and click on Create. Finally, copy the generated snippet text on the next page.

To run `python etl_web_to_gcs.py`, open a remote terminal and check the logs in the terminal. You can also view the logs in the Prefect UI under Flow Runs > Logs.

```
14:15:07.351 | INFO    | prefect.engine - Created flow run 'ivory-hippo' for flow 'etl-web-to-gcs'
14:15:07.557 | INFO    | Flow run 'ivory-hippo' - Created task run 'fetch-b4598a4a-0' for task 'fetch'
14:15:07.560 | INFO    | Flow run 'ivory-hippo' - Executing 'fetch-b4598a4a-0' immediately...
/home/senat/prefect-zoomcamp/flows/02_gcp/etl_web_to_gcs.py:14: DtypeWarning: Columns (6) have mixed types. Specify dtype option on import or set low_memory=False.
  df = pd.read_csv(dataset_url)
14:15:13.219 | INFO    | Task run 'fetch-b4598a4a-0' - Finished in state Completed()
14:15:13.275 | INFO    | Flow run 'ivory-hippo' - Created task run 'clean-b9fd7e03-0' for task 'clean'
14:15:13.277 | INFO    | Flow run 'ivory-hippo' - Executing 'clean-b9fd7e03-0' immediately...
14:15:13.979 | INFO    | Task run 'clean-b9fd7e03-0' -    VendorID  ... congestion_surcharge
0       1.0  ...                  2.5
1       1.0  ...                  0.0

[2 rows x 18 columns]
14:15:13.981 | INFO    | Task run 'clean-b9fd7e03-0' - columns: VendorID                        float64
tpep_pickup_datetime     datetime64[ns]
tpep_dropoff_datetime    datetime64[ns]
passenger_count                 float64
trip_distance                   float64
RatecodeID                      float64
store_and_fwd_flag               object
PULocationID                      int64
DOLocationID                      int64
payment_type                    float64
fare_amount                     float64
extra                           float64
mta_tax                         float64
tip_amount                      float64
tolls_amount                    float64
improvement_surcharge           float64
total_amount                    float64
congestion_surcharge            float64
dtype: object
14:15:13.982 | INFO    | Task run 'clean-b9fd7e03-0' - rows: 1369765
14:15:14.019 | INFO    | Task run 'clean-b9fd7e03-0' - Finished in state Completed()
14:15:14.072 | INFO    | Flow run 'ivory-hippo' - Created task run 'write_local-f322d1be-0' for task 'write_local'
14:15:14.074 | INFO    | Flow run 'ivory-hippo' - Executing 'write_local-f322d1be-0' immediately...
14:15:19.284 | INFO    | Task run 'write_local-f322d1be-0' - Finished in state Completed()
14:15:19.332 | INFO    | Flow run 'ivory-hippo' - Created task run 'write_gcs-1145c921-0' for task 'write_gcs'
14:15:19.333 | INFO    | Flow run 'ivory-hippo' - Executing 'write_gcs-1145c921-0' immediately...
14:15:19.482 | INFO    | Task run 'write_gcs-1145c921-0' - Getting bucket 'dtc_data_lake_exalted-point-376315'.
14:15:19.679 | INFO    | Task run 'write_gcs-1145c921-0' - Uploading from PosixPath('data/yellow/yellow_tripdata_2021-01.parquet') to the bucket 'dtc_data_lake_exalted-point-376315' path 'data/yellow/yellow_tripdata_2021-01.parquet'.
14:15:20.239 | INFO    | Task run 'write_gcs-1145c921-0' - Finished in state Completed()
14:15:20.282 | INFO    | Flow run 'ivory-hippo' - Finished in state Completed('All states completed.')
```

# From Google Cloud Storage to Big Query

BigQuery is a cloud-based data warehouse provided by Google Cloud Platform that allows users to store, manage, query, and analyze massive volumes of data using a SQL-like interface. It is designed to handle petabyte-scale datasets and perform fast, ad-hoc queries in real-time. It integrates with other Google Cloud services like Cloud Storage and Cloud Dataflow to provide a seamless end-to-end data processing pipeline. Additionally, it supports features like machine learning, geospatial analysis, and advanced analytics through the use of SQL extensions and user-defined functions.

```python
from pathlib import Path
import pandas as pd
from prefect import flow, task
from prefect_gcp.cloud_storage import GcsBucket
from prefect_gcp import GcpCredentials


@task(retries=3)
def extract_from_gcs(color: str, year: int, month: int) -> Path:
    """Download trip data from GCS"""
    gcs_path = f"data/{color}/{color}_tripdata_{year}-{month:02}.parquet"
    gcs_block = GcsBucket.load("zoom-gcs")
    gcs_block.get_directory(from_path = gcs_path, local_path = f"../data/")
    return Path(f"../data/{gcs_path}")


@task()
def transform(path: Path) -> pd.DataFrame:
    """Data cleaning example"""
    df = pd.read_parquet(path)
    print(f"pre: missing passenger count: {df['passenger_count'].isna().sum()}")
    df["passenger_count"].fillna(0, inplace=True)
    print(f"post: missing passenger count: {df['passenger_count'].isna().sum()}")
    return df


@task()
def write_bq(df: pd.DataFrame) -> None:
    """Write DataFrame to BiqQuery"""

    gcp_credentials_block = GcpCredentials.load("zoom-gcp-creds")

    df.to_gbq(
        destination_table = "dezoomcamp.rides",
        project_id = "exalted-point-376315",
        credentials = gcp_credentials_block.get_credentials_from_service_account(),
        chunksize = 500_000, 
        if_exists = "append",
    )


@flow()
def etl_gcs_to_bq():
    """Main ETL flow to load data into Big Query"""
    color = "yellow"
    year = 2021
    month = 1

    path = extract_from_gcs(color, year, month)
    df = transform(path)
    write_bq(df)


if __name__ == "__main__":
    etl_gcs_to_bq()
```

A `task` decorator is used to define a function `extract_from_gcs()`, which downloads trip data from Google Cloud Storage (GCS) using the `GcsBucket` block from `prefect_gcp.cloud_storage`. The function takes three arguments - color, year, and month - which are used to construct the path to the desired file. The `get_directory` method is used to download all files from a specified directory path in a GCS bucket to a specified local directory path. In this particular line, it is downloading a single file from a specific directory in the GCS bucket using the `from_path` argument and saving it to a local directory path using the `local_path` argument. The function returns a Path object representing the local path of the downloaded file.

Another `task` decorator is used to define a function `transform()`, which performs data cleaning on the downloaded file. The function takes a `Path` object as its only argument, reads the parquet file into a pandas dataframe, replaces any missing values in the `passenger_count` column with 0, and returns the cleaned dataframe.

`isna()` is a pandas method used to check if a value in a dataframe is missing or not. It returns a boolean value (True or False) indicating if the value is missing (NaN or None) or not.

`fillna()` is another pandas method that is used to fill missing or NaN values in a dataframe with a specified value. The `inplace=True` parameter applies the changes to the existing DataFrame.

A final `task` decorator is used to define a function `write_bq()`, which writes the cleaned dataframe to BigQuery using the `to_gbq()` method from pandas. The function takes the cleaned dataframe as its only argument and uses the `GcpCredentials` block from `prefect_gcp` to authenticate the write operation. `gcp_credentials_block.get_credentials_from_service_account()` is a method that returns a credentials object that can be used to authenticate access to GCP resources, such as BigQuery. `df.to_gbq()` is a method in Pandas that allows a DataFrame to be written to a Google BigQuery table. The method uses the Google Cloud Python library, `google-cloud-bigquery`, to load the DataFrame into BigQuery.

The `to_gbq()` method takes several arguments, including the destination table name, project ID, and the BigQuery credentials. The method also allows specifying whether the data should be appended to the existing table, replaced, or fail if the table already exists. Additionally, the method also supports chunking the data when uploading it to BigQuery, which can be useful for uploading large datasets.

A `flow` decorator is used to define the main ETL flow `etl_gcs_to_bq()`, which combines the `extract_from_gcs()`, `transform()`, and `write_bq()` tasks. The flow specifies the input parameters `color`, `year`, and `month`, which are used by the `extract_from_gcs()` task to construct the GCS file path. The flow then executes the tasks in order, passing the output of one task as input to the next.

Overall, this ETL flow download trip data from GCS, perform data cleaning, and write the cleaned data to BigQuery.

Also to load data from Google Cloud Storage (GCS) to Google BigQuery (GBQ), you can use the GBQ web console. First, navigate to the console and search for "BigQuery." Once in the BigQuery web UI, click the "Create table" button and select GCS as the data source. Then, fill out the required fields in the GUI, such as the GCS path to the data and the schema for the table. Once you've entered all of the necessary information, click "Create table" to load the data into GBQ.

Run `python etl_gcs_to_bq.py` in remote terminal and you can check in Prefect UI

Run these queries to check data in GBQ

SELECT COUNT(*) FROM `PROJECT_NAME.dezoomcamp.rides` 

SELECT * FROM `PROJECT_NAME.dezoomcamp.rides` LIMIT 1000
# Parametrizing Flow & Deployments

Now, we will add parameterization to our Prefect flows and create deployments. We will be building upon the `etl_web_to_gsc.py` file, so let's create a new file called `parameterized_flow.py`.

## Parametrizing the script from your flow

```python
@flow()
def etl_web_to_gcs(year: int, month: int, color: str) -> None:
    """The main ETL function"""
    dataset_file = f"{color}_tripdata_{year}-{month:02}"
    dataset_url = f"https://github.com/DataTalksClub/nyc-tlc-data/releases/download/{color}/{dataset_file}.csv.gz"

    df = fetch(dataset_url)
    df_clean = clean(df)
    path = write_local(df_clean, color, dataset_file)
    write_gcs(path)


@flow()
def etl_parent_flow(
    months: list[int] = [1, 2], year: int = 2021, color: str = "yellow"
):
    for month in months:
        etl_web_to_gcs(year, month, color)


if __name__ == "__main__":
    color = "yellow"
    months = [1, 2, 3]
    year = 2021
    etl_parent_flow(months, year, color)
```
First we are add parametters to `etl_web_to_gcs` fuction

This code defines two Prefect flows: `etl_web_to_gcs` and `etl_parent_flow`.

`etl_web_to_gcs` takes three parameters: `year`, `month`, and `color`. It uses these parameters to construct a URL to download a specific dataset, fetches the dataset, cleans it, writes it to a local file, and then writes it to Google Cloud Storage.

`etl_parent_flow` takes three parameters as well, but sets default values for `months`, `year`, and `color`. It loops over the provided months and calls `etl_web_to_gcs` for each month.

Finally, the code sets some variables (`color`, `months`, and `year`) and calls `etl_parent_flow` with these variables as arguments.

This allows you to parameterize your flows and create more complex workflows that call other flows with different parameters.

```python
from prefect.tasks import task_input_hash
from datetime import timedelta


@task(retries=3, cache_key_fn=task_input_hash, cache_expiration=timedelta(days=1))
def fetch(dataset_url: str) -> pd.DataFrame:
    """Read taxi data from web into pandas DataFrame"""
    df = pd.read_csv(dataset_url)
    return df
```
Add the `cache_key_fn` to the `fetch` function for generate a unique identifier for the task based on its inputs.

## Prefect Deployment

https://docs.prefect.io/latest/concepts/deployments/

Prefect Deployment refers to the process of deploying a Prefect Flow to an environment where it can be scheduled and executed, such as a cloud-based infrastructure like AWS or GCP, or an on-premise server. Deploying a Prefect Flow involves packaging up the code, dependencies, and any required configurations into a deployable unit, which can then be executed in a target environment.

The process of deployment typically involves creating a container image or executable file that includes the Prefect Flow and any required dependencies. This can be done using tools like Docker or Kubernetes, which allow for easy containerization and orchestration of the deployment process. Once the container or executable is built, it can be deployed to the target environment and scheduled for execution according to a desired schedule or trigger.

There are two methods to create a deployment: via CLI command or using Python. In this case, we'll use the CLI to create a deployment. 

`prefect deployment build ./parameterized_flow.py:etl_parent_flow -n "Parameterized ETL"`

After executing the CLI command, a YAML file containing all the deployment details is generated. This file serves as the metadata, and the parameters can be adjusted in the YAML file or in the UI after applying the changes. In this case, let's adjust the parameters in the YAML file by adding `{"color": "yellow", "months" :[1, 2, 3], "year": 2021}`.

Once the YAML file has been updated, we can apply the deployment by running `prefect deployment apply etl_parent_flow-deployment.yaml.`

## Prefect Deployments on UI

If you go to the Orion user interface, you'll be able to see that the deployment has been created. After creating the deployment, you can modify various parameters or add new details using the UI. Once you have made the desired changes, you can run the deployment.

After initiating a quick run, the flow will enter the "Scheduled" state under "Flow runs". To execute this workflow, we need to set up an agent for deployment.
## Prefect Work Queues and Agents

https://docs.prefect.io/latest/concepts/work-pools/

[What’s the role of agents and work queues](https://discourse.prefect.io/t/whats-the-role-of-agents-and-work-queues-and-how-the-concept-of-agents-differ-between-prefect-1-0-and-2-0/689)

Prefect Work Queues and Agents are components of the Prefect workflow orchestration platform that allow you to schedule, manage, and execute your workflows in a distributed environment.

A work queue is a message queue system that holds the tasks that need to be executed. Work queues are used to decouple the processing of a task from the application that submits the task. In the context of Prefect, work queues are used to store and distribute tasks across a cluster of computing resources.

An agent is a process that runs on a computing resource, such as a server or a container, and is responsible for executing tasks that are assigned to it by the Prefect server. Agents monitor a work queue for new tasks and pull them off the queue for execution. Prefect provides several types of agents, including LocalAgent, DockerAgent, KubernetesAgent, and AWSBatchAgent, which are designed to work with different computing environments.

Together, work queues and agents allow you to distribute your workflows across multiple computing resources, which can significantly improve performance and scalability.

To run the workflow scheduled in the previous step, we need to launch an agent. The following command will start the agent and configure it to run the workflow automatically: 

`prefect agent start --work-queue "default"`

## Notifications

Users can configure notifications to be sent via various channels, including email, Slack, PagerDuty, and more. These notifications can alert users when a workflow run has started, completed, failed, or been skipped, and can provide details such as the run ID, flow name, and any relevant logs or errors. Notifications can help users stay informed and quickly respond to any issues that arise during workflow execution.

# Schedules & Docker Storage with Infrastructure

## Scheduling a deployment
## Flow code storage
## Running tasks in Docker

# Prefect Cloud and Additional Resources

## Using Prefect Cloud instead of local Prefect
## Workspaces
## Running flows on GCP