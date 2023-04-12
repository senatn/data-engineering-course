# Introduction to Docker

Docker is an open-source platform for developing, shipping, and running applications in containers. A container is a lightweight and portable executable package of software that includes everything needed to run an application, such as code, libraries, dependencies, and configurations. Docker allows developers to create and manage containers easily, enabling them to develop applications on any platform, package them with all their dependencies, and deploy them to any environment without any compatibility issues. Docker provides a standardized way to package, distribute, and run applications, making it easier for developers to build and deploy their applications.

### Why should we care about Docker?
* Reproducibality
* Local Experiments
* Integration tests (CI/CD)
* Running pipelines on the cloud (AWS Batch, Kubernetes jobs)
* Spark (for defining data pipelines)
* Serverless (AWS Lamda, Google functions)

### Docker Commands

Usage:  
```
docker [OPTIONS] [COMMAND] [ARG...]
```

Example:
```
sudo service docker start
sudo service docker status
docker --help
docker run hello-world
docker create hello-world
docker start hello-world
docker ps -a
docker container rm -f hello-world
docker image rm -f hello-world
docker image ls -a
docker container prune
docker run -it python:3.9
docker run -it ubuntu bash
docker build -t test:pandas .
```
`-f` flag forces the action

`-t` flag attaching the bash process to our terminal

`-i` is interactive

`-d` is detach, which means you just run the container and then detach from it. Essentially, you run container in the background.

`-a`  all

### Let's Start Using Docker

First, its good to be read this [create a new container](https://docs.docker.com/engine/reference/commandline/create/) page in docker documentation.

We need to use Python and the Pandas library in our first container. To do that, we can use the `docker run -it python:3.9` command. However, there's a problem. When we use the interactive mode and start using Python with Docker in the bash, we can't install Pandas in that specific container. Of course, we can exit the Python prompt with `ctrl+d` and use the `pip install pandas` command in the bash, but doing so will only install Pandas locally, not in the container. So we need a way to use the bash prompt to install Pandas in our container. If we use the `docker run -it --entrypoint=bash python:3.9` command, we will have a bash prompt instead of a Python prompt in our container. Now, we can run `pip install pandas` in this specific container to install Pandas, and after that, with the `python` command, we can execute things.

When we leave the container and run the docker `run -it python:3.9` command again, Docker will create a new container with the same image. We can use the ` docker container start [OPTIONS] CONTAINER [CONTAINER...]` (or shorthand: `docker start <container name or id>`) command to start the container at any point.

### Dockerfile

[What is a Dockerfile?](https://docs.docker.com/engine/reference/builder/)

Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. 

[When should I use Dockerfile?](https://www.simplilearn.com/tutorials/docker-tutorial/what-is-dockerfile#:~:text=One%20should%20use%20a%20Dockerfile,server%20you%20are%20working%20on.)

One should use a Dockerfile when there’s a need to distribute/collaborate on the app’s operating system with a team. Use Dockerfile as the version control system for the entire app’s OS. Or use Dockerfile to run the code to the laptop in the same environment as the server you are working on. 



This is our first ```Dockerfile```. It looks very simple right now.

```dockerfile
FROM python:3.9

RUN pip install pandas

ENTRYPOINT [ "bash" ]
```

We use the following command while in the directory where the `Dockerfile` file is located.
```
docker build -t test:pandas .
```
The `docker build` command builds Docker images from a `Dockerfile`. Image name will be 'test' and we can add a tag   (pandas) and '` .`' means, build image in this directory.

And we create a container with this image with following command.
```
docker run -it test:pandas
```

### Create a Pipeline


We create a file named `pipeline.py` in the current directory.

```py
import sys

import pandas as pd

# fancy stuff with pandas

print(sys.argv)

first_arg = sys.argv[1]

print(f"hello, your first command line argument is ---> {first_arg}")
```

And modify `Dockerfile`

```dockerfile
FROM python:3.9

RUN pip install pandas

WORKDIR /app
COPY pipeline.py pipeline.py

ENTRYPOINT [ "python", "pipeline.py" ]
```

`WORKDIR /app` is changes the working directory location in the docker container.

`COPY pipeline.py pipeline.py` Copies pipeline.py from our local to app/pipeline.py in the container

`ENTRYPOINT [ "python", "pipeline.py" ]` When we runing `docker run` it start with python interpreter and runs pipeline.py

# Ingesting NY Taxi Data to Postgres

Before create a Postgres container I had to create a local docker volume because when I run my docker code with my local file path I got an error. But first...

[What is Docker Volume?](https://docs.docker.com/storage/volumes/)

Docker volume is a mechanism to persist data outside a Docker container's filesystem. Volumes provide a way to store data that can be shared between containers and host systems. Volumes can be mounted to a container at runtime using the `--mount` or `-v` option when running the `docker run` command. This allows data to persist even if the container is deleted or recreated, and enables easy sharing of data between containers.

The docker volume seems important to us, but there is one more important thing we need to know.

Docker is stateless!!

The containers running in a Docker environment do not maintain any persistent data or state. This means that any data or changes to the container's filesystem are lost once the container is deleted or stopped. To persist data, it is common to use Docker volumes or bind mounts to store data outside the container's filesystem, so that it can be reused and shared between containers.

OK now let's get back to create a docker container for Postgres. I created an `ny_taxi_postgres_data` folder in my current directory and asked postgres to copy the data to this folder, which will run in the docker container.

```
docker run -it \
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v /mnt/c/Users/..../data-engineering-course/week_1/2_docker_sql/ny_taxi_postgres_data:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:13
```
But this code gave me the following error.

```
chmod: changing permissions of '/var/lib/postgresql/data': Operation not permitted
The files belonging to this database system will be owned by user "postgres".
This user must also own the server process.

The database cluster will be initialized with locale "en_US.utf8".
The default database encoding has accordingly been set to "UTF8".
The default text search configuration will be set to "english".

Data page checksums are disabled.

fixing permissions on existing directory /var/lib/postgresql/data ... 
initdb: error: could not change permissions of directory 
"/var/lib/postgresql/data": Operation not permitted
```
For this error, I found 2 solutions that worked for me.
1) [The error is gone when host path is in WSL filesystem.](https://github.com/docker-library/postgres/issues/116#issuecomment-610732280)
```
  ...
  -v /home/..../ny_taxi_postgres_data:/var/lib/postgresql/data \
  ...
```
2) [Change that path by /var/lib/postgres/whatever and it will work.](https://stackoverflow.com/questions/50916801/kubernetes-pod-which-encapsulates-db-is-crashing)
```
  ...
  -v /mnt/c/Users/..../ny_taxi_postgres_data:/var/lib/postgresql/sena \
  ...
```

But neither of them fully explains the cause or solution of the error, and I did not want to use it. So I tried the solution in [Data Engineering Zoomcamp FAQ](https://docs.google.com/document/d/19bnYs80DwuUimHM65UV3sylsCn2j1vziPOwzBwQrebw/edit#heading=h.okhwu85s6cwk) and I created a local volume.

```
docker volume create --name dtc_postgres_volume_local -d local
```

Than I create a postgres container with this environmental variables
```
docker run -it\
  -e POSTGRES_USER="root" \
  -e POSTGRES_PASSWORD="root" \
  -e POSTGRES_DB="ny_taxi" \
  -v dtc_postgres_volume_local:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:13
  ```

 Finally
  ```
  database system is ready to accept connections
  ```
And now we can try accessing this database

# Connecting pgAdmin and Postgres

`pgcli` is a command-line interface (CLI) tool for PostgreSQL, an open-source relational database management system. `pgcli` provides an interface for running SQL queries, autocompletion, syntax highlighting, and other features to make it easier to interact with PostgreSQL databases. 

```
pip install pgcli
```
```
pgcli -h localhost -p 5432 -u root -d ny_taxi
```
After entering the password (root), we are now connected to postgres. We can test the connection with `\dt` command.

### Downloading The NY Taxi Data

Download the data 

```
wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz
```
Looking for text data
```
less yellow_tripdata_2021-01.csv
```
```
head -n 100 yellow_tripdata_2021-01.csv
```
`wc` means 'world count' and following command gives us count of lines (1369766)
```
wc -l yellow_tripdata_2021-01.csv
```
### Reading CSV with Pandas

I will use jupyter lab. Jupyter Lab is a web-based interactive development environment (IDE) for working with Jupyter notebooks, code, and data.
```
jupyter-lab
```
Create a new notebook named "upload-data"

The notes of this part are in the [upload-data.ipynb](https://github.com/senatn/data-engineering-course/blob/main/week_1/2_docker_sql/upload-data.ipynb) file.

### Use pgAdmin

`pgAdmin` is a free and open-source administration and management tool for the PostgreSQL relational database management system. It provides a graphical user interface for managing and interacting with PostgreSQL databases, allowing users to perform tasks such as creating, modifying, and querying databases, managing users and permissions, and monitoring server performance. It supports a wide range of features, including a SQL editor, server-side query tool, data export and import, and more.

This time we will use `pgAdmin` with docker instead of downloading it directly. In this case, we will need to connect the postres database, which is a docker container, and the `pgAdmin` container. We will use docker-network to connect two containers together.

Docker-network refers to the virtual network created by Docker to enable communication between Docker containers and with the host system. Docker uses networks to isolate and connect containers, allowing them to communicate with each other and with the host system as needed.
 
Docker provides several types of networks, such as bridge, host, and overlay, each with its own characteristics and use cases. For example, a bridge network is used to connect containers within the same host, while an overlay network is used to connect containers across multiple Docker hosts. The network can also be customized to meet specific requirements, such as adding additional network drivers or configuring network settings.

Using Docker networks is a fundamental aspect of running and managing Docker containers, and is essential for building complex, multi-container applications.

First we have to stop postgres container.

Create a network

```
docker create pg-network
```
Now we will create a new postgres container using this network.

```
docker run -it \
    -e POSTGRES_USER="root" \
    -e POSTGRES_PASSWORD="root" \
    -e POSTGRES_DB="ny_taxi" \
    -v dtc_postgres_volume_local:/var/lib/postgresql/data \
    -p 5432:5432 \
    --network=pg-network \
    --name pg-database \
    postgres:13
```
Thanks to the volume! We will be able to use the data in the new container as well. let's check this out.

```
$ pgcli -h localhost -p 5432 -u root -d ny_taxi
```
```
root@localhost:ny_taxi> SELECT COUNT(1) FROM yellow_taxi_data;
+---------+
| count   |
|---------|
| 1369765 |
+---------+
SELECT 1
Time: 1.093s (1 second), executed in: 1.067s (1 second)
root@localhost:ny_taxi>

```
We still have all the data and now we need pgAdmin container with same network
```
docker run -it \
    -e PGADMIN_DEFAULT_EMAIL="admin@admin.com" \
    -e PGADMIN_DEFAULT_PASSWORD="root" \
    -p 8080:80 \
    --network=pg-network \
    --name pgadmin \
    dpage/pgadmin4
```

Go to ""localhost:8080"" in the browser and login. Create a new server with using container variables. Now we can navigate to our data.

# Data ingestion With Python Script

```
jupyter nbconvert --to=script upload-data.ipynb
```
Clean and organize the "upload-data.py" and rename script to "ingest_data.py". If we look at this file we can see following code. This means that the data downloaded from the URL will be saved in a file named 'output.csv'
```py
if url.endswith('.csv.gz'):
    csv_name = 'output.csv.gz'
else:
    csv_name = 'output.csv'
```
We will also see that the library named argparse is used.

`argparse` is a standard Python library that makes it easy to write user-friendly command-line interfaces. It provides a simple and flexible way to specify and parse command-line arguments and options.`argparse` then takes care of parsing the command-line arguments passed to your script, checking for required arguments, and automatically generating usage and error messages.

Drop the current table in pgAdmin
```
DROP TABLE yellow_taxi_data
```
Run the script
```
URL="https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz"

python3 ingest_data.py \
  --user=root \
  --password=root \
  --host=localhost \
  --port=5432 \
  --db=ny_taxi \
  --table_name=yellow_taxi_trips \
  --url=${URL}
  ```

This way we put the code that we have in jupyter notebook in a script an than we execute it. To check, we can make a query from pgAdmin.
```
SELECT COUNT(1) FROM yellow_taxi_trips;
```
### Data ingestion With Docker

For dockerize the ingestion we need to update `Dockerfile` and build a new image with this. 
```Dockerfile
FROM python:3.9

RUN apt-get install wget
RUN pip install pandas sqlalchemy psycopg2

WORKDIR /app
COPY ingest_data.py ingest_data.py

ENTRYPOINT [ "python", "ingest_data.py" ]
```

`psycopg2` is a popular PostgreSQL database adapter for the Python programming language. It allows you to connect to and interact with a PostgreSQL database from Python, allowing you to execute SQL statements, manage transactions, and interact with the database in a variety of ways.

`SQLAlchemy` is a Python library that provides a set of high-level API for connecting to relational databases. It aims to simplify the process of connecting to databases and executing SQL queries, making it easier to interact with databases in a Pythonic way. `SQLAlchemy` provides a comprehensive set of constructs for representing tables, columns, and relationships, and includes an ORM (Object Relational Mapper) that allows you to interact with your database in a more object-oriented way, rather than writing raw SQL queries.

Build a image
```
docker build -t taxi_ingest:001 .
```
Create a container with taxi_ingest:001 image
```
URL="https://github.com/DataTalksClub/nyc-tlc-data/releases/download/yellow/yellow_tripdata_2021-01.csv.gz"

docker run -it \
  --network=pg-network \
  taxi_ingest:001 \
    --user=root \
    --password=root \
    --host=pg-database \
    --port=5432 \
    --db=ny_taxi \
    --table_name=yellow_taxi_trips \
    --url=${URL}
```
To check, we can make a query from pgAdmin.
```
SELECT COUNT(1) FROM yellow_taxi_trips;
```
# Docker Compose

So far, we have created separate Docker commands for Postgres and pgAdmin using a network, but this is not an efficient solution. Typically, docker compose is used for multi-container configurations. Docker Compose is a tool for defining and running multi-container Docker applications. It allows you to define your application's services, networks, and volumes in a single file, called a docker-compose.yml file, and then start and stop all services from this file. This makes it easier to manage and configure multiple containers as a single application, rather than managing each container individually.

```yml
services:
  pgdatabase:
    image: postgres:13
    environment:
      - POSTGRES_USER=root
      - POSTGRES_PASSWORD=root
      - POSTGRES_DB=ny_taxi
    volumes:
      - "dtc_postgres_volume_local:/var/lib/postgresql/data:rw"
    ports:
      - "5432:5432"
  pgadmin:
    image: dpage/pgadmin4
    environment:
      - PGADMIN_DEFAULT_EMAIL=admin@admin.com
      - PGADMIN_DEFAULT_PASSWORD=root
    ports:
      - "8080:80"
```

```
docker compose up
```

At this point, I ran into a couple of problems that I had to deal with. I used local volume in the container I created for postgres before. That's why I tried to use this local volume in the .yml codes above. Because my data was in this local volume. But when i ran the above codes I got this message.
```
service "pgdatabase" refers to undefined volume dtc_postgres_volume_local: invalid compose project
```
The reason for this message that when using volume, we need to specify the volumes in the .ylm file, just like we specify the services. ("If the mount is [a host path](https://github.com/compose-spec/compose-spec/blob/master/spec.md#volumes) and only used by a single service, it MAY be declared as part of the service definition instead of the top-level volumes key.")

After docker-compose and local volume worked without any errors, I got an error like "could not translate host name 'pg-database' " while creating a server in pgAdmin. So I implemented the solution suggested in the Data Engineering Zoomcamp FAQ.

“Try: new host name, best without “ - ” e.g. pgdatabase

And on docker-compose.yml, should specify docker network & specify the same network in both  containers “

I deleted all the containers and changed the host name to pgdatabase and specified network in .yml.

Here is the final .yml that works fine for me
```yml
services:
  pgdatabase:
    image: postgres:13
    environment:
      - POSTGRES_USER=root
      - POSTGRES_PASSWORD=root
      - POSTGRES_DB=ny_taxi
    volumes:
      - dtc_postgres_volume_local:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    networks:
      - pg-network
  pgadmin:
    image: dpage/pgadmin4
    environment:
      - PGADMIN_DEFAULT_EMAIL=admin@admin.com
      - PGADMIN_DEFAULT_PASSWORD=root
    ports:
      - "8080:80"
    networks:
      - pg-network
volumes:
  dtc_postgres_volume_local:
    external: true
networks:
  pg-network:
    name: pg-network
```

Voilà <3