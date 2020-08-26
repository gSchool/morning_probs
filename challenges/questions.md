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
##### !end-question

##### !placeholder

```sql
SELECT a.* FROM taxi_trips AS a
INNER JOIN (SELECT rate_code_id, MAX(tip_amount/fare_amount) AS high
			FROM taxi_trips
			WHERE fare_amount != 0
			GROUP BY rate_code_id) AS b
ON b.rate_code_id = a.rate_code_id
AND b.high = a.tip_amount/a.fare_amount
WHERE fare_amount != 0
ORDER BY a.rate_code;
```

##### !end-placeholder

##### !tests
SELECT a.* FROM taxi_trips a
inner join (SELECT rate_code_id, MAX(tip_amount/fare_amount) AS high
			FROM taxi_trips
			WHERE fare_amount !=0
			GROUP BY rate_code_id) b
on b.rate_code_id = a.rate_code_id
AND b.high = a.tip_amount/a.fare_amount
WHERE fare_amount != 0
ORDER BY a.rate_code;
##### !end-tests

### !hint
You can do a join on 2 things
### !end-hint

### !hint
You can use table_name.* to only select columns FROM one of your tables
### !end-hint

#### !explanation
```sql
SELECT a.* FROM taxi_trips AS a
inner join (SELECT rate_code_id, MAX(tip_amount/fare_amount) AS high
			FROM taxi_trips
			WHERE fare_amount != 0
			GROUP BY rate_code_id) AS b
on b.rate_code_id = a.rate_code_id
AND b.high = a.tip_amount/a.fare_amount
WHERE fare_amount != 0
ORDER BY a.rate_code;
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
Return all trip information from trips that had a higher than average fare amount. Order by fare amount descending
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
