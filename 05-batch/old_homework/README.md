## Module 5 Homework  (DRAFT)

Solution: https://www.youtube.com/watch?v=YtddC7vJOgQ

In this homework we'll put what we learned about Spark in practice.

For this homework we will be using the FHV 2019-10 data found here. [FHV Data](https://github.com/DataTalksClub/nyc-tlc-data/releases/download/fhv/fhv_tripdata_2019-10.csv.gz)

### Question 1: 

**Install Spark and PySpark** 

- Install Spark
- Run PySpark
- Create a local spark session
- Execute spark.version.

What's the output?

**3.5.4**

> [!NOTE]
> To install PySpark follow this [guide](https://github.com/DataTalksClub/data-engineering-zoomcamp/blob/main/05-batch/setup/pyspark.md)

### Question 2: 

**FHV October 2019**

Read the October 2019 FHV into a Spark Dataframe with a schema as we did in the lessons.

Repartition the Dataframe to 6 partitions and save it to parquet.

What is the average size of the Parquet (ending with .parquet extension) Files that were created (in MB)? Select the answer which most closely matches.

- 1MB
- **6MB <-**
- 25MB
- 87MB

```python
fhv_spark_df.repartition(6).write.parquet('/home/jsrl/notebooks/data/pq/hw5/')
!ls -lh /home/jsrl/notebooks/data/pq/hw5/
total 39M
-rw-r--r-- 1 jsrl jsrl    0 Feb 14 19:49 _SUCCESS
-rw-r--r-- 1 jsrl jsrl 6.4M Feb 14 19:49 part-00000-ed96f45d-0669-41de-a972-4f99dd019ec6-c000.snappy.parquet
-rw-r--r-- 1 jsrl jsrl 6.4M Feb 14 19:49 part-00001-ed96f45d-0669-41de-a972-4f99dd019ec6-c000.snappy.parquet
-rw-r--r-- 1 jsrl jsrl 6.4M Feb 14 19:49 part-00002-ed96f45d-0669-41de-a972-4f99dd019ec6-c000.snappy.parquet
-rw-r--r-- 1 jsrl jsrl 6.4M Feb 14 19:49 part-00003-ed96f45d-0669-41de-a972-4f99dd019ec6-c000.snappy.parquet
-rw-r--r-- 1 jsrl jsrl 6.4M Feb 14 19:49 part-00004-ed96f45d-0669-41de-a972-4f99dd019ec6-c000.snappy.parquet
-rw-r--r-- 1 jsrl jsrl 6.4M Feb 14 19:49 part-00005-ed96f45d-0669-41de-a972-4f99dd019ec6-c000.snappy.parquet
```

### Question 3: 

**Count records** 

How many taxi trips were there on the 15th of October?

Consider only trips that started on the 15th of October.

- 108,164
- 12,856
- 452,470
- **62,610 <-**

```python
fhv_parquet_df.createOrReplaceTempView('homework5')
#Question 3
spark.sql("""
SELECT COUNT(*)
FROM homework5
WHERE cast(pickup_datetime as date) = '2019-10-15'
""").show()
```

> [!IMPORTANT]
> Be aware of columns order when defining schema

### Question 4: 

**Longest trip for each day** 

What is the length of the longest trip in the dataset in hours?

- **631,152.50 Hours <-** 
- 243.44 Hours
- 7.68 Hours
- 3.32 Hours

```python
#Question 4
spark.sql("""
SELECT pickup_datetime
,dropOff_datetime
,(unix_timestamp(dropOff_datetime) - unix_timestamp(pickup_datetime)) /3600 as hours
FROM homework5
ORDER BY hours desc
""").show()
```

### Question 5: 

**User Interface**

Spark’s User Interface which shows the application's dashboard runs on which local port?

- 80
- 443
- **4040 <-**
- 8080


### Question 6: 

**Least frequent pickup location zone**

Load the zone lookup data into a temp view in Spark</br>
[Zone Data](https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv)

Using the zone lookup data and the FHV October 2019 data, what is the name of the LEAST frequent pickup location Zone?</br>

- East Chelsea
- **Jamaica Bay <-**
- Union Sq
- Crown Heights North

```python
spark.sql("""
SELECT count(*) as cnt
,PUlocationID
,Borough
,zone
FROM homework5 inner join lookup
on PUlocationID = LocationID
GROUP BY PUlocationID, Borough, zone
ORDER BY cnt asc
""").show()
```


## Submitting the solutions

- Form for submitting: https://courses.datatalks.club/de-zoomcamp-2024/homework/hw5
- Deadline: See the website
