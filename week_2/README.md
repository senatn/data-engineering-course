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

The UI also has features such as Deployments, Workqueues, Blocks, Notifications, and TaskRun Concurrency. Although we will discuss Deployments and Workqueues in a later video, let's briefly touch on Blocks, Notifications, and TaskRun Concurrency. TaskRun Concurrency can be configured for tasks by adding a tag to the task and setting a limit through a CLI command.

Notifications are essential for keeping us informed when something goes wrong with our system. Instead of monitoring the dashboard frequently, we can set up notifications to alert us when an issue occurs that requires investigation.

