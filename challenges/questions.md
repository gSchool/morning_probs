# Queries on the Taxi Trip Database

#### The Taxi Trip database contains various information about individual trips taken in taxis. It contains a table called taxi_trips and the structure is below

```sql
CREATE TABLE taxi_trips (
    trip_id bigserial PRIMARY KEY,
    vendor_id varchar(1) NOT NULL,
    tpep_pickup_datetime timestamp with time zone NOT NULL,
    tpep_dropoff_datetime timestamp with time zone NOT NULL,
    passenger_count integer NOT NULL,
    trip_distance numeric(8,2) NOT NULL,
    pickup_longitude numeric(18,15) NOT NULL,
    pickup_latitude numeric(18,15) NOT NULL,
    rate_code_id varchar(2) NOT NULL,
    store_and_fwd_flag varchar(1) NOT NULL,
    dropoff_longitude numeric(18,15) NOT NULL,
    dropoff_latitude numeric(18,15) NOT NULL,
    payment_type varchar(1) NOT NULL,
    fare_amount numeric(9,2) NOT NULL,
    extra numeric(9,2) NOT NULL,
    mta_tax numeric(5,2) NOT NULL,
    tip_amount numeric(9,2) NOT NULL,
    tolls_amount numeric(9,2) NOT NULL,
    improvement_surcharge numeric(9,2) NOT NULL,
    total_amount numeric(9,2) NOT NULL
```

#### Let's look at some questions dealing with the taxi database


# !challenge

* type: code-snippet
* language: sql
* id: 24ca18aa-db61-4054-899f-a7756114bcf7
* title: Average Trip Distance by vendor
* data_path: /DATA/taxis.sql



##### !question
Return the average distance traveled per trip by each of the vendors in taxi_trips. Round your answers to 2 decimals. Order by vendor_id.
Your answer should look like this:

```
 vendor_id | round 
-----------+-------
 1         |  3.81
 2         |  4.49
(2 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT vendor_id, ROUND(AVG(trip_distance),2) FROM taxi_trips
GROUP BY vendor_id
ORDER BY vendor_id;
```

##### !end-placeholder

##### !tests
SELECT vendor_id, ROUND(AVG(trip_distance),2) FROM taxi_trips
GROUP BY vendor_id
ORDER BY vendor_id;
##### !end-tests


### !hint
GROUP BY vendor
### !end-hint


#### !explanation
```sql
SELECT vendor_id, ROUND(AVG(trip_distance),2) FROM taxi_trips
GROUP BY vendor_id
ORDER BY vendor_id;
```
#### !end-explanation

# !end-challenge



# !challenge

* type: code-snippet
* language: sql
* id: 946c2c4e-e2b8-4854-a3f9-927b4d3e038a
* title: Average Speed of Driver per Trip
* data_path: /DATA/taxis.sql



##### !question
Calculate the speed of the driver in MPH for each trip, rounded to 2 decimal places. You will need to use the pickup and dropoff times to figure out the length of time of each trip. You will also need the distance traveled. Exclude rows WHERE time or distance is 0. 

Your answer should look like this:
```
   mph   
---------
    0.02
    0.15
    1.33
    1.33
    1.68
    1.71
    1.71
    3.92
    4.01
    4.44
...
(988 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT ROUND((trip_distance / EXTRACT(epoch FROM (tpep_dropoff_datetime - tpep_pickup_datetime))*3600)::numeric,2) AS mph
FROM taxi_trips
WHERE trip_distance != 0 AND EXTRACT(epoch FROM (tpep_dropoff_datetime - tpep_pickup_datetime)) != 0
ORDER BY mph;
```

##### !end-placeholder

##### !tests
SELECT ROUND((trip_distance / EXTRACT(epoch FROM (tpep_dropoff_datetime - tpep_pickup_datetime))*3600)::numeric,2) AS mph
FROM taxi_trips
WHERE trip_distance != 0 AND EXTRACT(epoch FROM (tpep_dropoff_datetime - tpep_pickup_datetime)) != 0
ORDER BY mph;

##### !end-tests


### !hint
When you subtract the times, you get an interval, use: select EXTRACT(epoch FROM (tpep_dropoff_datetime - tpep_pickup_datetime)) to get the time in seconds. Distance traveled (in miles) is given, so just divide distance by time to get speed, but don't forget to convert back to MPH.
### !end-hint

### !hint
You need to exclude rows WHERE either distance or time traveled is 0.
### !end-hint

### !hint
You'll need to cast your double precision data to numeric before rounding. The speed calculation you did gives double precision data.
### !end-hint

#### !explanation
```sql
SELECT ROUND((trip_distance / EXTRACT(epoch FROM (tpep_dropoff_datetime - tpep_pickup_datetime))*3600)::numeric,2) AS mph
FROM taxi_trips
WHERE trip_distance != 0 AND EXTRACT(epoch FROM (tpep_dropoff_datetime - tpep_pickup_datetime)) != 0
ORDER BY mph;
```
#### !end-explanation

# !end-challenge



# !challenge

* type: code-snippet
* language: sql
* id: 483b8f3d-6016-439b-8a24-b8604c115c43
* title: Average Cost/Rider for Multi-Rider trips
* data_path: /DATA/taxis.sql



##### !question
Calculate the average cost per rider for all trips where there was more than 1 rider. Round the answer to 2 decimal places (the answer is one average for all riders on these trips)

Your answer should look like this:
```
 avg_cost 
-------
  6.90
(1 row)
```
##### !end-question

##### !placeholder

```sql
SELECT ROUND(AVG(total_amount / passenger_count),2)
FROM taxi_trips
WHERE passenger_count > 1;
```

##### !end-placeholder

##### !tests
SELECT ROUND(AVG(total_amount / passenger_count),2)
FROM taxi_trips
WHERE passenger_count > 1;
##### !end-tests


### !hint

### !end-hint


#### !explanation
```sql
SELECT ROUND(AVG(total_amount / passenger_count),2)
FROM taxi_trips
WHERE passenger_count > 1
```
#### !end-explanation

# !end-challenge


# !challenge

* type: code-snippet
* language: sql
* id: cfdafbe7-4439-4856-bd5d-195f5179ba4f
* title: Highest Tip % by rate code
* data_path: /DATA/taxis.sql



##### !question
Find the highest tip percentage (use fare amount AS denominator) for each rate code, order by rate_code. Return rate_code_id and the percentage rounded to two decimal points.

Your answer should look like this:
```
rate_code_id | max_tip_percentage 
--------------+--------------------
 1            |              75.00
 2            |              28.04
 4            |               0.00
 5            |              20.18
(4 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT 
    rate_code_id, 
    ROUND(MAX(tip_amount/fare_amount)*100, 2) AS MAX_TIP_PERCENTAGE
FROM taxi_trips
WHERE fare_amount != 0
GROUP BY rate_code_id
ORDER BY rate_code_id
```

##### !end-placeholder

##### !tests
SELECT 
    rate_code_id, 
    ROUND(MAX(tip_amount/fare_amount)*100, 2) AS MAX_TIP_PERCENTAGE
FROM taxi_trips
WHERE fare_amount != 0
GROUP BY rate_code_id
ORDER BY rate_code_id
##### !end-tests


### !hint
WHERE fare amount is not 0
### !end-hint


#### !explanation
```sql
SELECT 
    rate_code_id, 
    ROUND(MAX(tip_amount/fare_amount)*100, 2) AS MAX_TIP_PERCENTAGE
FROM taxi_trips
WHERE fare_amount != 0
GROUP BY rate_code_id
ORDER BY rate_code_id
```
#### !end-explanation

# !end-challenge


# !challenge

* type: code-snippet
* language: sql
* id: d83a1786-31d1-4ba4-a979-24ee15c841aa
* title: Show all trip info WHERE tip % was highest for its rate code
* data_path: /DATA/taxis.sql



##### !question
Return all columns for the trips that had the highest tip percentage in their rate code AS found above. Order by rate code.

Your answer should look like this:
```
 trip_id | vendor_id |  tpep_pickup_datetime  | tpep_dropoff_datetime  | passenger_count | trip_distance |  pickup_longitude   |  pickup_latitude   | rate_code_id | store_and_fwd_flag |  dropoff_longitude  |  dropoff_latitude  | payment_type | fare_amount | extra | mta_tax | tip_amount | tolls_amount | improvement_surcharge | total_amount 
---------+-----------+------------------------+------------------------+-----------------+---------------+---------------------+--------------------+--------------+--------------------+---------------------+--------------------+--------------+-------------+-------+---------+------------+--------------+-----------------------+--------------
     720 | 1         | 2016-06-01 04:04:29+00 | 2016-06-01 04:07:04+00 |               2 |          0.30 | -73.990715026855470 | 40.665405273437510 | 1            | N                  | -73.987976074218750 | 40.662563323974610 | 1            |        4.00 |  0.50 |    0.50 |       3.00 |         0.00 |                  0.30 |         8.30
     446 | 2         | 2016-06-01 04:02:51+00 | 2016-06-01 04:36:12+00 |               1 |         18.98 | -73.776679992675780 | 40.645339965820310 | 2            | N                  | -73.995887756347660 | 40.748691558837890 | 1            |       52.00 |  0.00 |    0.50 |      14.58 |         5.54 |                  0.30 |        72.92
     984 | 2         | 2016-06-01 04:06:02+00 | 2016-06-01 04:45:49+00 |               1 |         25.64 | -73.862991333007810 | 40.769359588623050 | 4            | N                  | -73.769943237304670 | 41.035110473632810 | 2            |       90.50 |  0.50 |    0.50 |       0.00 |         5.54 |                  0.30 |        97.34
     818 | 1         | 2016-06-01 04:05:05+00 | 2016-06-01 04:05:25+00 |               0 |          6.20 | -73.924842834472660 | 40.766197204589844 | 5            | N                  | -73.924842834472660 | 40.766197204589844 | 1            |       27.01 |  0.00 |    0.00 |       5.45 |         0.00 |                  0.30 |        32.76
(4 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT a.* FROM taxi_trips a
inner join (SELECT rate_code_id, MAX(tip_amount/fare_amount) AS high
			FROM taxi_trips
			WHERE fare_amount !=0
			GROUP BY rate_code_id) AS b
on b.rate_code_id = a.rate_code_id
AND b.high = a.tip_amount/a.fare_amount
WHERE fare_amount != 0
ORDER BY a.rate_code_id;
```

##### !end-placeholder

##### !tests
SELECT a.* FROM taxi_trips a
inner join (SELECT rate_code_id, MAX(tip_amount/fare_amount) AS high
			FROM taxi_trips
			WHERE fare_amount !=0
			GROUP BY rate_code_id) AS b
on b.rate_code_id = a.rate_code_id
AND b.high = a.tip_amount/a.fare_amount
WHERE fare_amount != 0
ORDER BY a.rate_code_id;
##### !end-tests

### !hint
You can do a join on 2 things
### !end-hint

### !hint
You can use table_name.* to only select columns FROM one of your tables
### !end-hint

#### !explanation
```sql
SELECT a.* FROM taxi_trips a
inner join (SELECT rate_code_id, MAX(tip_amount/fare_amount) AS high
			FROM taxi_trips
			WHERE fare_amount !=0
			GROUP BY rate_code_id) AS b
on b.rate_code_id = a.rate_code_id
AND b.high = a.tip_amount/a.fare_amount
WHERE fare_amount != 0
ORDER BY a.rate_code_id;
```
#### !end-explanation

# !end-challenge



# !challenge

* type: code-snippet
* language: sql
* id: b129d770-d45f-49ff-85cb-377fb2e8b99b
* title: CASE WHEN
* data_path: /DATA/taxis.sql



##### !question
Return all the original columns as well as a new column called 'duration' that contains "Long" if the trip was over 15miles, and 'Short' otherwise for each trip. Order by duration.

Your answer should look like this:
```
 trip_id | vendor_id |  tpep_pickup_datetime  | tpep_dropoff_datetime  | passenger_count | trip_distance |  pickup_longitude   |  pickup_latitude   | rate_code_id | store_and_fwd_flag |  dropoff_longitude  |  dropoff_latitude  | payment_type | fare_amount | extra | mta_tax | tip_amount | tolls_amount | improvement_surcharge | total_amount | duration 
---------+-----------+------------------------+------------------------+-----------------+---------------+---------------------+--------------------+--------------+--------------------+---------------------+--------------------+--------------+-------------+-------+---------+------------+--------------+-----------------------+--------------+----------
     301 | 2         | 2016-06-01 04:01:56+00 | 2016-06-01 04:34:33+00 |               1 |         21.37 | -73.782371520996100 | 40.644584655761720 | 2            | N                  | -73.979278564453120 | 40.776905059814450 | 1            |       52.00 |  0.00 |    0.50 |      11.67 |         5.54 |                  0.30 |        70.01 | Long
      35 | 1         | 2016-06-01 04:00:13+00 | 2016-06-01 04:29:04+00 |               1 |         17.50 | -73.786155700683580 | 40.642078399658200 | 2            | N                  | -73.947021484375010 | 40.810623168945310 | 2            |       52.00 |  0.00 |    0.50 |       0.00 |         5.54 |                  0.30 |        58.34 | Long
      64 | 1         | 2016-06-01 04:00:24+00 | 2016-06-01 04:22:41+00 |               1 |         16.30 | -73.789695739746100 | 40.646621704101560 | 2            | N                  | -73.975791931152340 | 40.744895935058594 | 1            |       52.00 |  0.00 |    0.50 |      11.65 |         5.54 |                  0.30 |        69.99 | Long
     535 | 1         | 2016-06-01 04:03:22+00 | 2016-06-01 04:30:45+00 |               1 |         17.20 | -73.781257629394530 | 40.644969940185550 | 2            | N                  | -73.958862304687500 | 40.761547088623050 | 2            |       52.00 |  0.00 |    0.50 |       0.00 |         0.00 |                  0.30 |        52.80 | Long
     374 | 1         | 2016-06-01 04:02:26+00 | 2016-06-01 04:30:22+00 |               1 |         18.40 | -73.781921386718750 | 40.644599914550780 | 1            | N                  | -74.011985778808600 | 40.576694488525390 | 2            |       50.00 |  0.50 |    0.50 |       0.00 |         0.00 |                  0.30 |        51.30 | Long
     951 | 2         | 2016-06-01 04:05:49+00 | 2016-06-01 04:34:48+00 |               6 |         19.07 | -73.782417297363280 | 40.644538879394530 | 1            | N                  | -73.952316284179690 | 40.676269531250000 | 1            |       52.50 |  0.50 |    0.50 |      10.76 |         0.00 |                  0.30 |        64.56 | Long
     952 | 2         | 2016-06-01 04:05:50+00 | 2016-06-01 04:44:09+00 |               1 |         20.32 | -73.987808227539060 | 40.718582153320310 | 1            | N                  | -73.930824279785160 | 40.597896575927730 | 1            |       55.50 |  0.50 |    0.50 |      11.36 |         0.00 |                  0.30 |        68.16 | Long
     603 | 2         | 2016-06-01 04:03:47+00 | 2016-06-01 04:39:24+00 |               5 |         18.10 | -73.976455688476560 | 40.750663757324220 | 2            | N                  | -73.776222229003900 | 40.645545959472656 | 2            |       52.00 |  0.00 |    0.50 |       0.00 |         0.00 |                  0.30 |        52.80 | Long
     641 | 1         | 2016-06-01 04:04:04+00 | 2016-06-01 04:27:36+00 |               1 |         15.30 | -73.789451599121100 | 40.646762847900390 | 1            | N                  | -73.954818725585940 | 40.719818115234375 | 1            |       42.00 |  0.50 |    0.50 |       8.65 |         0.00 |                  0.30 |        51.95 | Long
     762 | 1         | 2016-06-01 04:04:45+00 | 2016-06-01 04:29:27+00 |               1 |         17.50 | -73.802482604980480 | 40.647724151611330 | 2            | N                  | -73.948287963867200 | 40.783882141113280 | 1            |       52.00 |  0.00 |    0.50 |      10.00 |         5.54 |                  0.30 |        68.34 | Long
...
(1000 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT 
  *,
  CASE 
	  WHEN trip_distance > 15 THEN 'Long'
	  ELSE 'Short'
  END AS duration
FROM taxi_trips
ORDER BY duration
```

##### !end-placeholder

##### !tests
SELECT 
  *,
  CASE 
	  WHEN trip_distance > 15 THEN 'Long'
	  ELSE 'Short'
  END AS duration
FROM taxi_trips
ORDER BY duration

##### !end-tests



### !hint
Don't forget the END in your CASE WHEN
### !end-hint

#### !explanation
```sql
SELECT 
  *,
  CASE 
	  WHEN trip_distance > 15 THEN 'Long'
	  ELSE 'Short'
  END AS duration
FROM taxi_trips
ORDER BY duration
```
#### !end-explanation

# !end-challenge


# !challenge

* type: code-snippet
* language: sql
* id: 0cb98eeb-fb40-4f09-a23e-44c1a0e38eea
* title: Revenues by vendor by rate code
* data_path: /DATA/taxis.sql



##### !question
Find the total revenues by rate_code_id for each vendor. Order by vendor id ascending and total revenues descending (Columns should be vendor, rate_code, revenues)
Your answer should look like this:
```
 vendor_id | rate_code_id | total_revenues 
-----------+--------------+----------------
 1         | 1            |        5747.00
 1         | 2            |         780.00
 1         | 5            |          77.02
 2         | 1            |        7060.50
 2         | 2            |        1040.00
 2         | 5            |         583.59
 2         | 4            |          90.50
(7 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT vendor_id, rate_code_id, SUM(fare_amount) AS total_revenues
FROM taxi_trips
GROUP BY vendor_id, rate_code_id
ORDER BY 1,3 DESC
```

##### !end-placeholder

##### !tests
SELECT vendor_id, rate_code_id, SUM(fare_amount)
FROM taxi_trips
GROUP BY vendor_id, rate_code_id
ORDER BY 1,3 DESC
##### !end-tests


### !hint

### !end-hint

#### !explanation
```sql
SELECT vendor_id, rate_code_id, SUM(fare_amount)
FROM taxi_trips
GROUP BY vendor_id, rate_code_id
ORDER BY 1,3 DESC
```
#### !end-explanation

# !end-challenge

# !challenge

* type: code-snippet
* language: sql
* id: 331230ee-98dc-4866-bd8d-d9e848e1498c
* title: Trips with higher than average fare amounts
* data_path: /DATA/taxis.sql



##### !question
Return all trip information from trips that had a higher than average fare amount. Order by fare amount descending.
Your answer should look like this:
```
 trip_id | vendor_id |  tpep_pickup_datetime  | tpep_dropoff_datetime  | passenger_count | trip_distance |  pickup_longitude   |  pickup_latitude   | rate_code_id | store_and_fwd_flag |  dropoff_longitude  |  dropoff_latitude  | payment_type | fare_amount | extra | mta_tax | tip_amount | tolls_amount | improvement_surcharge | total_amount 
---------+-----------+------------------------+------------------------+-----------------+---------------+---------------------+--------------------+--------------+--------------------+---------------------+--------------------+--------------+-------------+-------+---------+------------+--------------+-----------------------+--------------
     508 | 2         | 2016-06-01 04:03:09+00 | 2016-06-01 04:48:41+00 |               1 |         36.42 | -73.787796020507810 | 40.643913269042970 | 5            | N                  | -73.852027893066400 | 41.047401428222656 | 1            |      163.00 |  0.00 |    0.00 |       0.00 |         5.54 |                  0.30 |       168.84
     729 | 2         | 2016-06-01 04:04:33+00 | 2016-06-01 04:06:09+00 |               1 |          0.00 | -73.961265563964830 | 40.583755493164060 | 5            | N                  | -73.961257934570300 | 40.583740234375000 | 2            |      100.00 |  0.00 |    0.50 |       0.00 |         0.00 |                  0.30 |       100.80
     984 | 2         | 2016-06-01 04:06:02+00 | 2016-06-01 04:45:49+00 |               1 |         25.64 | -73.862991333007810 | 40.769359588623050 | 4            | N                  | -73.769943237304670 | 41.035110473632810 | 2            |       90.50 |  0.50 |    0.50 |       0.00 |         5.54 |                  0.30 |        97.34
     671 | 2         | 2016-06-01 04:04:14+00 | 2016-06-01 04:19:23+00 |               1 |         11.09 | -73.692817687988300 | 40.681598663330080 | 5            | N                  | -73.564292907714840 | 40.716606140136720 | 1            |       88.50 |  0.00 |    0.00 |       0.00 |         0.00 |                  0.30 |        88.80
     629 | 2         | 2016-06-01 04:03:58+00 | 2016-06-01 04:04:13+00 |               1 |          0.00 | -74.003692626953120 | 40.807498931884770 | 5            | N                  | -74.003692626953120 | 40.807498931884770 | 1            |       80.00 |  0.00 |    0.00 |       0.00 |         0.00 |                  0.30 |        80.30
     669 | 2         | 2016-06-01 04:04:12+00 | 2016-06-01 04:05:34+00 |               1 |          0.00 | -73.789596557617190 | 40.667221069335940 | 5            | N                  | -73.789596557617190 | 40.667209625244150 | 1            |       72.09 |  0.00 |    0.00 |       0.00 |         0.00 |                  0.30 |        72.39
     448 | 2         | 2016-06-01 04:02:51+00 | 2016-06-01 04:35:32+00 |               1 |         24.43 | -73.862930297851550 | 40.769348144531250 | 1            | N                  | -73.951911926269550 | 40.586059570312490 | 2            |       65.00 |  0.50 |    0.50 |       0.00 |         0.00 |                  0.30 |        66.30
     844 | 1         | 2016-06-01 04:05:14+00 | 2016-06-01 04:44:52+00 |               2 |         21.90 | -73.776710510253900 | 40.645381927490240 | 1            | N                  | -74.021049499511730 | 40.633296966552734 | 1            |       63.50 |  0.50 |    0.50 |       8.00 |         0.00 |                  0.30 |        72.80
      41 | 1         | 2016-06-01 04:00:16+00 | 2016-06-01 04:36:25+00 |               1 |         23.50 | -73.776687622070300 | 40.645217895507810 | 1            | N                  | -73.894950866699230 | 40.865486145019530 | 2            |       63.50 |  0.50 |    0.50 |       0.00 |         5.54 |                  0.30 |        70.34
     429 | 1         | 2016-06-01 04:02:46+00 | 2016-06-01 04:34:00+00 |               1 |         23.70 | -73.789184570312500 | 40.647388458251950 | 1            | N                  | -74.004226684570300 | 40.650524139404300 | 1            |       62.50 |  0.50 |    0.50 |      12.75 |         0.00 |                  0.30 |        76.55
...
(325 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT * FROM taxi_trips
WHERE fare_amount > (SELECT AVG(fare_amount) FROM taxi_trips)
ORDER BY fare_amount DESC
```

##### !end-placeholder

##### !tests
SELECT * FROM taxi_trips
WHERE fare_amount > (SELECT AVG(fare_amount) FROM taxi_trips)
ORDER BY fare_amount DESC
##### !end-tests


### !hint

### !end-hint

#### !explanation
```sql
SELECT * FROM taxi_trips
WHERE fare_amount > (SELECT AVG(fare_amount) FROM taxi_trips)
ORDER BY fare_amount DESC
```
#### !end-explanation

# !end-challenge

# !challenge

* type: code-snippet
* language: sql
* id: 5c11e149-fcd5-4548-b344-058e87be66ea
* title: Trips with a fare amount within 10pct of the average
* data_path: /DATA/taxis.sql



##### !question
Return all trips that had a fare amount within 10pct of the average fare amount for all the trips. 
Your answer should look like this:
```
 trip_id | vendor_id |  tpep_pickup_datetime  | tpep_dropoff_datetime  | passenger_count | trip_distance |  pickup_longitude   |  pickup_latitude   | rate_code_id | store_and_fwd_flag |  dropoff_longitude  |  dropoff_latitude  | payment_type | fare_amount | extra | mta_tax | tip_amount | tolls_amount | improvement_surcharge | total_amount 
---------+-----------+------------------------+------------------------+-----------------+---------------+---------------------+--------------------+--------------+--------------------+---------------------+--------------------+--------------+-------------+-------+---------+------------+--------------+-----------------------+--------------
     528 | 1         | 2016-06-01 04:03:20+00 | 2016-06-01 04:19:51+00 |               1 |          4.60 | -73.989151000976560 | 40.718734741210940 | 1            | N                  | -73.950752258300780 | 40.668140411376950 | 1            |       16.50 |  0.50 |    0.50 |       2.00 |         0.00 |                  0.30 |        19.80
     983 | 1         | 2016-06-01 04:06:02+00 | 2016-06-01 04:18:25+00 |               1 |          4.90 | -74.014259338378900 | 40.713352203369130 | 1            | N                  | -73.983703613281250 | 40.729610443115230 | 1            |       16.50 |  0.50 |    0.50 |       3.55 |         0.00 |                  0.30 |        21.35
     565 | 1         | 2016-06-01 04:03:35+00 | 2016-06-01 04:17:12+00 |               1 |          4.80 | -73.982223510742190 | 40.731830596923830 | 1            | N                  | -73.941635131835940 | 40.786808013916016 | 1            |       16.50 |  0.50 |    0.50 |       1.00 |         0.00 |                  0.30 |        18.80
     689 | 1         | 2016-06-01 04:04:19+00 | 2016-06-01 04:18:51+00 |               1 |          4.60 | -73.955383300781250 | 40.799659729003906 | 1            | N                  | -73.933906555175780 | 40.846801757812490 | 1            |       16.50 |  0.50 |    0.50 |       3.55 |         0.00 |                  0.30 |        21.35
     260 | 1         | 2016-06-01 04:01:40+00 | 2016-06-01 04:18:48+00 |               1 |          4.50 | -74.001892089843740 | 40.739589691162100 | 1            | N                  | -73.953330993652340 | 40.779571533203130 | 1            |       16.50 |  0.50 |    0.50 |       1.20 |         0.00 |                  0.30 |        19.00
     702 | 2         | 2016-06-01 04:04:23+00 | 2016-06-01 04:20:43+00 |               3 |          4.89 | -73.985229492187500 | 40.741878509521490 | 1            | N                  | -73.917213439941400 | 40.759838104248050 | 1            |       16.50 |  0.50 |    0.50 |       4.67 |         5.54 |                  0.30 |        28.01
     111 | 1         | 2016-06-01 04:00:45+00 | 2016-06-01 04:17:55+00 |               2 |          4.60 | -73.953804016113280 | 40.770946502685550 | 1            | N                  | -73.953453063964830 | 40.822097778320310 | 1            |       16.50 |  0.50 |    0.50 |       3.55 |         0.00 |                  0.30 |        21.35
     135 | 1         | 2016-06-01 04:00:55+00 | 2016-06-01 04:20:35+00 |               1 |          4.20 | -74.005577087402340 | 40.740722656250000 | 1            | N                  | -73.961814880371100 | 40.713790893554690 | 1            |       16.50 |  0.50 |    0.50 |       3.55 |         0.00 |                  0.30 |        21.35
     511 | 2         | 2016-06-01 04:03:11+00 | 2016-06-01 04:19:51+00 |               4 |          4.70 | -73.986213684082030 | 40.767368316650390 | 1            | N                  | -74.011520385742190 | 40.714328765869130 | 2            |       16.50 |  0.50 |    0.50 |       0.00 |         0.00 |                  0.30 |        17.80
     473 | 2         | 2016-06-01 04:03:00+00 | 2016-06-01 04:17:12+00 |               2 |          4.67 | -73.986152648925780 | 40.756080627441406 | 1            | N                  | -73.992301940917970 | 40.712871551513670 | 2            |       16.50 |  0.50 |    0.50 |       0.00 |         0.00 |                  0.30 |        17.80
...
(90 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT * FROM taxi_trips
WHERE fare_amount between (SELECT AVG(fare_amount)*.9 FROM taxi_trips) 
AND (SELECT AVG(fare_amount)*1.1 FROM taxi_trips)
ORDER BY fare_amount DESC
```

##### !end-placeholder

##### !tests
SELECT * FROM taxi_trips
WHERE fare_amount between (SELECT AVG(fare_amount)*.9 FROM taxi_trips) 
AND (SELECT AVG(fare_amount)*1.1 FROM taxi_trips)
ORDER BY fare_amount DESC
##### !end-tests


### !hint
Use between
### !end-hint

#### !explanation
```sql
SELECT * FROM taxi_trips
WHERE fare_amount between (SELECT AVG(fare_amount)*.9 FROM taxi_trips) 
AND (SELECT AVG(fare_amount)*1.1 FROM taxi_trips)
ORDER BY fare_amount DESC
```
#### !end-explanation

# !end-challenge

# !challenge

* type: code-snippet
* language: sql
* id: beeab2f6-2260-4f37-ae05-427ee5d193c3
* title: Payment type totals
* data_path: /DATA/taxis.sql



##### !question
Return 3 columns for each trip: the fare_amount, the payment_type, and the sum of all the fare_amounts for the same payment type of that row. (Look up the syntax for a window function, it will create a subtotal by payment_type). Order by payment type.
Your answer should look like this:
```
 fare_amount | payment_type |   sum    
-------------+--------------+----------
       13.00 | 1            | 10480.60
       19.50 | 1            | 10480.60
       11.50 | 1            | 10480.60
        9.50 | 1            | 10480.60
       52.00 | 1            | 10480.60
       21.00 | 1            | 10480.60
        5.00 | 1            | 10480.60
       17.00 | 1            | 10480.60
        9.50 | 1            | 10480.60
       13.00 | 1            | 10480.60
...
(1000 rows)
```
##### !end-question

##### !placeholder

```sql
SELECT fare_amount, payment_type,
       SUM(fare_amount) OVER
         (PARTITION BY payment_type ORDER BY payment_type)
FROM taxi_trips
```

##### !end-placeholder

##### !tests
SELECT fare_amount, payment_type,
       SUM(fare_amount) OVER
         (PARTITION BY payment_type ORDER BY payment_type)
FROM taxi_trips
##### !end-tests


### !hint
       SUM(fare_amount) OVER
         (PARTITION BY payment_type ORDER BY payment_type)
this should be what your window function is doing
### !end-hint

#### !explanation
```sql
SELECT fare_amount, payment_type,
       SUM(fare_amount) OVER
         (PARTITION BY payment_type ORDER BY payment_type)
FROM taxi_trips
```
#### !end-explanation

# !end-challenge
