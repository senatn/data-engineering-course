## Week 1 Homework

## Question 1. Knowing docker tags

Which tag has the following text? - *Write the image ID to the file* 

- `--iidfile string`


## Question 2. Understanding docker first run 

Run docker with the python:3.9 image in an interactive mode and the entrypoint of bash.
Now check the python modules that are installed ( use pip list). 
How many python packages/modules are installed?

```
$ docker run -it --entrypoint=bash python:3.9
root@9d40d0f2a106:/# pip list
Package    Version
---------- -------
pip        22.0.4
setuptools 58.1.0
wheel      0.38.4
```
- 3

# Prepare Postgres

Download this data and put it into Postgres (with jupyter notebooks or with a pipeline)
```
 wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-01.csv.gz
 ```
 ```
 wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv
 ```
 After downloading the data, I will import it using Jupyter into Postgres.

 Jupyter notebook codes are here [upload-data.ipynb]()

## Question 3. Count records 

How many taxi trips were totally made on January 15?

```sql
SELECT COUNT(1)
FROM green_tripdata
WHERE lpep_pickup_datetime::date = '2019-01-15'
AND lpep_dropoff_datetime::date = '2019-01-15';
```

- 20530

## Question 4. Largest trip for each day

Which was the day with the largest trip distance
Use the pick up time for your calculations.

```sql
SELECT lpep_pickup_datetime,
MAX(trip_distance)
FROM green_tripdata
GROUP BY lpep_pickup_datetime
ORDER BY 2 DESC
LIMIT 1;
```

- 2019-01-15

## Question 5. The number of passengers

In 2019-01-01 how many trips had 2 and 3 passengers?

```sql
SELECT passenger_count, COUNT(1) AS trip_count
FROM green_tripdata
WHERE date(lpep_pickup_datetime) = '2019-01-01'
GROUP BY 1
HAVING passenger_count in (2, 3);
```

- 2: 1282 ; 3: 254



## Question 6. Largest tip

For the passengers picked up in the Astoria Zone which was the drop off zone that had the largest tip?
We want the name of the zone, not the id.

```sql

```

- Central Park
- Jamaica
- South Ozone Park
- Long Island City/Queens Plaza

