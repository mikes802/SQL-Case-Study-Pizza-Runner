# 8-Week SQL Challenge: Case Study #2 - Pizza Runner

This is Case Study #2 from the 8-Week SQL Challenge of [Danny Ma's Serious SQL course](https://www.datawithdanny.com/ "Data With Danny"). My SQL [exploration](#data-exploration) and [solutions](#sql-query-solutions) can be found after the [background](#background) section below.

## Table of Contents
1. [Background](#background)
2. [Data Exploration](#data-exploration)
3. [SQL Query Solutions](#sql-query-solutions)
    1. [Pizza Metrics](#pizza-metrics)
    2. [Runner and Customer Experience](#runner-and-customer-experience)

## [Background](#table-of-contents)
### Problem Statement


## [Data Exploration](#table-of-contents)
### `runners`
```sql
-- Check data types for `runners` table

SELECT
  table_schema,
  table_name,
  column_name,
  data_type
FROM information_schema.columns
WHERE 
  table_schema = 'pizza_runner'
  AND table_name = 'runners';
```
| table_schema | table_name | column_name       | data_type |
|--------------|------------|-------------------|-----------|
| pizza_runner | runners    | runner_id         | integer   |
| pizza_runner | runners    | registration_date | date      |

```sql
-- Explore the `members` table

SELECT *
FROM pizza_runner.runners;
```
| runner_id | registration_date        |
|-----------|--------------------------|
| 1         | 2021-01-01T00:00:00.000Z |
| 2         | 2021-01-03T00:00:00.000Z |
| 3         | 2021-01-08T00:00:00.000Z |
| 4         | 2021-01-15T00:00:00.000Z |

### `runner_orders`
```sql
-- Check data types for `runner_orders` table

SELECT
  table_schema,
  table_name,
  column_name,
  data_type
FROM information_schema.columns
WHERE
  table_schema = 'pizza_runner'
  AND table_name = 'runner_orders';
```
| table_schema | table_name    | column_name  | data_type         |
|--------------|---------------|--------------|-------------------|
| pizza_runner | runner_orders | order_id     | integer           |
| pizza_runner | runner_orders | runner_id    | integer           |
| pizza_runner | runner_orders | pickup_time  | character varying |
| pizza_runner | runner_orders | distance     | character varying |
| pizza_runner | runner_orders | duration     | character varying |
| pizza_runner | runner_orders | cancellation | character varying |

```sql
-- Explore the `runner_orders` table

SELECT *
FROM pizza_runner.runner_orders;
```
| order_id | runner_id | pickup_time         | distance | duration   | cancellation            |
|----------|-----------|---------------------|----------|------------|-------------------------|
| 1        | 1         | 2021-01-01 18:15:34 | 20km     | 32 minutes |                         |
| 2        | 1         | 2021-01-01 19:10:54 | 20km     | 27 minutes |                         |
| 3        | 1         | 2021-01-03 00:12:37 | 13.4km   | 20 mins    |                         |
| 4        | 2         | 2021-01-04 13:53:03 | 23.4     | 40         |                         |
| 5        | 3         | 2021-01-08 21:10:57 | 10       | 15         |                         |
| 6        | 3         | null                | null     | null       | Restaurant Cancellation |
| 7        | 2         | 2021-01-08 21:30:45 | 25km     | 25mins     | null                    |
| 8        | 2         | 2021-01-10 00:15:02 | 23.4 km  | 15 minute  | null                    |
| 9        | 2         | null                | null     | null       | Customer Cancellation   |
| 10       | 1         | 2021-01-11 18:50:20 | 10km     | 10minutes  | null                    |

Noted inconsistencies in the above table, specifically the `pickup_time`, `distance`, `duration`, and `cancellation` fields. The first three are string fields. Multiple values are entered as 'null' strings, which is different from an actual NULL. Caution needs to be taken with this table.

```sql
-- Check distinct values in `cancellation` column.

SELECT DISTINCT(cancellation)
FROM pizza_runner.runner_orders;
```
| cancellation            |
|-------------------------|
| _null_                  |
| null                    |
|                         |
| Customer Cancellation   |
| Restaurant Cancellation |

This shows that there are real `NULL`s, strings typed in as "null", and some just left blank. 

### `customer_orders`
```sql
-- Check data types for `customer_orders` table

SELECT
  table_schema,
  table_name,
  column_name,
  data_type
FROM information_schema.columns
WHERE
  table_schema = 'pizza_runner'
  AND table_name = 'customer_orders';
```
| table_schema | table_name      | column_name | data_type                   |
|--------------|-----------------|-------------|-----------------------------|
| pizza_runner | customer_orders | order_id    | integer                     |
| pizza_runner | customer_orders | customer_id | integer                     |
| pizza_runner | customer_orders | pizza_id    | integer                     |
| pizza_runner | customer_orders | exclusions  | character varying           |
| pizza_runner | customer_orders | extras      | character varying           |
| pizza_runner | customer_orders | order_time  | timestamp without time zone |

```sql
-- Explore the `customer_orders` table

SELECT *
FROM pizza_runner.customer_orders;
```
| order_id | customer_id | pizza_id | exclusions | extras | order_time               |
|----------|-------------|----------|------------|--------|--------------------------|
| 1        | 101         | 1        |            |        | 2021-01-01T18:05:02.000Z |
| 2        | 101         | 1        |            |        | 2021-01-01T19:00:52.000Z |
| 3        | 102         | 1        |            |        | 2021-01-02T23:51:23.000Z |
| 3        | 102         | 2        |            |        | 2021-01-02T23:51:23.000Z |
| 4        | 103         | 1        | 4          |        | 2021-01-04T13:23:46.000Z |
| 4        | 103         | 1        | 4          |        | 2021-01-04T13:23:46.000Z |
| 4        | 103         | 2        | 4          |        | 2021-01-04T13:23:46.000Z |
| 5        | 104         | 1        | null       | 1      | 2021-01-08T21:00:29.000Z |
| 6        | 101         | 2        | null       | null   | 2021-01-08T21:03:13.000Z |
| 7        | 105         | 2        | null       | 1      | 2021-01-08T21:20:29.000Z |
| 8        | 102         | 1        | null       | null   | 2021-01-09T23:54:33.000Z |
| 9        | 103         | 1        | 4          | 1, 5   | 2021-01-10T11:22:59.000Z |
| 10       | 104         | 1        | null       | null   | 2021-01-11T18:34:49.000Z |
| 10       | 104         | 1        | 2, 6       | 1, 4   | 2021-01-11T18:34:49.000Z |

Here I see more 'nulls' entered as string variables. The field `order_time` is a `TIMESTAMP` data type with no time zone.

```sql
-- Check distinct values in `extras` column.

SELECT DISTINCT(extras)
FROM pizza_runner.customer_orders;
```
| extras |
|--------|
| _null_ |
| null   |
| 1, 5   |
|        |
| 1, 4   |
| 1      |

As in the `runner_orders` table, there are variations of "null" here that need to be considered when querying. It's also important to note the different relationships represented in this table. One `order_id` can be related to many `pizza_id` values, and one `customer_id` can be related to multiple `order_id` values. 

### `pizza_names`
```sql
-- Check data types for `pizza_names` table

SELECT
  table_schema,
  table_name,
  column_name,
  data_type
FROM information_schema.columns
WHERE
  table_schema = 'pizza_runner'
  AND table_name = 'pizza_names';
```
| table_schema | table_name  | column_name | data_type |
|--------------|-------------|-------------|-----------|
| pizza_runner | pizza_names | pizza_id    | integer   |
| pizza_runner | pizza_names | pizza_name  | text      |

```sql
-- Explore the `pizza_names` table

SELECT *
FROM pizza_runner.pizza_names;
```
| pizza_id | pizza_name |
|----------|------------|
| 1        | Meatlovers |
| 2        | Vegetarian |

### `pizza_recipes`
```sql
-- Check data types for `pizza_recipes` table

SELECT
  table_schema,
  table_name,
  column_name,
  data_type
FROM information_schema.columns
WHERE
  table_schema = 'pizza_runner'
  AND table_name = 'pizza_recipes';
```
| table_schema | table_name    | column_name | data_type |
|--------------|---------------|-------------|-----------|
| pizza_runner | pizza_recipes | pizza_id    | integer   |
| pizza_runner | pizza_recipes | toppings    | text      |

```sql
-- Explore the `pizza_recipes` table

SELECT *
FROM pizza_runner.pizza_recipes;
```
| pizza_id | toppings                |
|----------|-------------------------|
| 1        | 1, 2, 3, 4, 5, 6, 8, 10 |
| 2        | 4, 6, 7, 9, 11, 12      |

### `pizza_toppings`
```sql
-- Check data types for `pizza_toppings` table

SELECT
  table_schema,
  table_name,
  column_name,
  data_type
FROM information_schema.columns
WHERE
  table_schema = 'pizza_runner'
  AND table_name = 'pizza_toppings';
```
| table_schema | table_name     | column_name  | data_type |
|--------------|----------------|--------------|-----------|
| pizza_runner | pizza_toppings | topping_id   | integer   |
| pizza_runner | pizza_toppings | topping_name | text      |

```sql
-- Explore the `pizza_toppings` table

SELECT *
FROM pizza_runner.pizza_toppings;
```
| topping_id | topping_name |
|------------|--------------|
| 1          | Bacon        |
| 2          | BBQ Sauce    |
| 3          | Beef         |
| 4          | Cheese       |
| 5          | Chicken      |
| 6          | Mushrooms    |
| 7          | Onions       |
| 8          | Pepperoni    |
| 9          | Peppers      |
| 10         | Salami       |
| 11         | Tomatoes     |
| 12         | Tomato Sauce |

## [SQL Query Solutions](#table-of-contents)
### [Pizza Metrics](#table-of-contents)
> 1. How many pizzas were ordered?

```sql
-- Each row in the `customer_orders` table represents one pizza.

SELECT COUNT(*)
FROM pizza_runner.customer_orders;
```
| count |
|-------|
| 14    |

> 2. How many unique customer orders were made?
```sql
-- Count number of distinct `order_id` values.

SELECT
  COUNT(DISTINCT order_id) AS order_count
FROM pizza_runner.customer_orders;
```
| unique_order_count |
|--------------------|
| 10                 |

> 3. How many successful orders were delivered by each runner?
```sql
-- Count number of rows that do not note a cancellation

SELECT
  runner_id,
  COUNT(*) AS successful_delivery_count
FROM pizza_runner.runner_orders
WHERE cancellation NOT IN ('Restaurant Cancellation', 'Customer Cancellation')
   OR cancellation IS NULL -- Need to include or will not return these rows in count
GROUP BY runner_id
ORDER BY runner_id;
```
| runner_id | successful_delivery_count |
|-----------|---------------------------|
| 1         | 4                         |
| 2         | 3                         |
| 3         | 1                         |

> 4. How many of each type of pizza was delivered?
```sql
-- Join successful delivery orders with # of pizza id's

WITH successful_deliveries AS (
  SELECT
    order_id
  FROM pizza_runner.runner_orders
  WHERE cancellation NOT IN ('Restaurant Cancellation', 'Customer Cancellation')
     OR cancellation IS NULL -- Need to include or will not return these rows
)
SELECT
  pizza_id,
  COUNT(pizza_id) AS pizza_count
FROM successful_deliveries
INNER JOIN pizza_runner.customer_orders -- There are multiple rows with same id
  ON successful_deliveries.order_id = customer_orders.order_id
GROUP BY pizza_id
ORDER BY pizza_id;
```
| pizza_id | pizza_count |
|----------|-------------|
| 1        | 9           |
| 2        | 3           |

> 5. How many Vegetarian and Meatlovers were ordered by each customer?
```sql
-- Use CASE WHEN to count number for each type per customer 

SELECT
  customer_id,
  SUM(
    CASE WHEN pizza_id = 1 THEN 1 ELSE 0
    END
  ) AS meatlovers_count,
  SUM(
    CASE WHEN pizza_id = 2 THEN 1 ELSE 0
    END
  ) AS vegetarian_count
FROM pizza_runner.customer_orders
GROUP BY customer_id
ORDER BY customer_id;
```
| customer_id | meatlovers_count | vegetarian_count |
|-------------|------------------|------------------|
| 101         | 2                | 1                |
| 102         | 2                | 1                |
| 103         | 3                | 1                |
| 104         | 3                | 0                |
| 105         | 0                | 1                |

> 6. What was the maximum number of pizzas delivered in a single order?
```sql
-- Successful delivery CTE 
WITH successful_deliveries AS (
  SELECT
    order_id
  FROM pizza_runner.runner_orders
  WHERE cancellation NOT IN ('Restaurant Cancellation', 'Customer Cancellation')
     OR cancellation IS NULL -- Need to include or will not return these rows
)
SELECT
  customer_orders.order_id,
  COUNT(customer_orders.order_id) AS pizza_count
FROM successful_deliveries
INNER JOIN pizza_runner.customer_orders -- There are multiple rows with same id
  ON successful_deliveries.order_id = customer_orders.order_id
GROUP BY customer_orders.order_id
ORDER BY pizza_count DESC
LIMIT 1;
```
| order_id | pizza_count |
|----------|-------------|
| 4        | 3           |

> 7. For each customer, how many delivered pizzas had at least 1 change and how many had no changes?
```sql
-- Fix the blank spaces and 'null' strings so that they are real NULLs
WITH fixed_nulls AS (
  SELECT
    order_id,
    customer_id,
    CASE WHEN exclusions IN ('', 'null') THEN NULL ELSE exclusions END AS exclusions,
    CASE WHEN extras IN ('', 'null') THEN NULL ELSE extras END AS extras
  FROM pizza_runner.customer_orders
)
SELECT
  customer_id,
  SUM(
    CASE WHEN (exclusions IS NOT NULL) OR (extras IS NOT NULL)  -- I'm looking for entries
         THEN 1
         ELSE 0
    END
  ) AS order_change_count,
  SUM(
    CASE WHEN (exclusions IS NULL) AND (extras IS NULL)  -- I'm looking for no entries
         THEN 1
         ELSE 0
    END
  ) AS no_order_change_count
FROM fixed_nulls
-- This will return only order_id's that had successful deliveries
WHERE EXISTS (
  SELECT 1
  FROM pizza_runner.runner_orders
  WHERE fixed_nulls.order_id = runner_orders.order_id
    AND (cancellation NOT IN ('Restaurant Cancellation', 'Customer Cancellation')
     OR cancellation IS NULL) -- Need to include or will not return these rows
)
GROUP BY customer_id
ORDER BY customer_id;
```
| customer_id | order_change_count | no_order_change_count |
|-------------|--------------------|-----------------------|
| 101         | 0                  | 2                     |
| 102         | 0                  | 3                     |
| 103         | 3                  | 0                     |
| 104         | 2                  | 1                     |
| 105         | 1                  | 0                     |

> 8. How many pizzas were delivered that had both exclusions and extras?
```sql
-- Fix the blank spaces and 'null' strings so that they are real NULLs
WITH fixed_nulls AS (
  SELECT
    order_id,
    customer_id,
    CASE WHEN exclusions IN ('', 'null') THEN NULL ELSE exclusions END AS exclusions,
    CASE WHEN extras IN ('', 'null') THEN NULL ELSE extras END AS extras
  FROM pizza_runner.customer_orders
)
SELECT
  COUNT(*) AS order_change_count
FROM fixed_nulls
WHERE EXISTS (
  SELECT 1
  FROM pizza_runner.runner_orders
  WHERE fixed_nulls.order_id = runner_orders.order_id
    AND (cancellation NOT IN ('Restaurant Cancellation', 'Customer Cancellation')
    OR cancellation IS NULL) -- Need to include or will not return these rows
)
  AND (exclusions IS NOT NULL AND extras IS NOT NULL);
```
| order_change_count |
|--------------------|
| 1                  |

> 9. What was the total volume of pizzas ordered for each hour of the day?
```sql
SELECT
  DATE_PART('HOUR', order_time) AS hour_of_day,
  COUNT(*) AS order_count
FROM pizza_runner.customer_orders
GROUP BY hour_of_day
ORDER BY hour_of_day;
```
| hour_of_day | order_count |
|-------------|-------------|
| 11          | 1           |
| 13          | 3           |
| 18          | 3           |
| 19          | 1           |
| 21          | 3           |
| 23          | 3           |

> 10. What was the volume of orders for each day of the week?
```sql
SELECT
  TO_CHAR(order_time, 'Day') AS day_of_week,
  COUNT(*) AS order_count
FROM pizza_runner.customer_orders
GROUP BY day_of_week, DATE_PART('dow', order_time)
ORDER BY DATE_PART('dow', order_time); -- This will order with Sunday at the top
```
| day_of_week | order_count |
|-------------|-------------|
| Sunday      | 1           |
| Monday      | 5           |
| Friday      | 5           |
| Saturday    | 3           |

### [Runner and Customer Experience](#table-of-contents)
> 1. How many runners signed up for each 1 week period? (i.e. week starts 2021-01-01)
```sql
SELECT
-- Use date truncation and offset by four days to get a custom start of week for Friday (2021-01-01)
  (DATE_TRUNC('WEEK', registration_date - INTERVAL '4 DAYS') + INTERVAL '4 DAYS')::DATE AS week_of,
  COUNT(*) AS runners_registered
FROM pizza_runner.runners
GROUP BY week_of
ORDER BY week_of;
```
| week_of                  | runners_registered |
|--------------------------|--------------------|
| 2021-01-01T00:00:00.000Z | 2                  |
| 2021-01-08T00:00:00.000Z | 1                  |
| 2021-01-15T00:00:00.000Z | 1                  |

> 2. What was the average time in minutes it took for each runner to arrive at the Pizza Runner HQ to pickup the order?
```sql
-- Will need to join `order_time` from `customer_orders` table to `runner_orders` table
-- `pickup_time` in `runner_orders` table is not a TIMESTAMP, unlike `order_time`

SELECT
  runner_id,
  AVG(
    (EXTRACT(EPOCH FROM pickup_time::TIMESTAMP)
      - EXTRACT(EPOCH FROM order_time)) / (60)
    ) AS avg_time_to_pickup_min
FROM pizza_runner.runner_orders
INNER JOIN pizza_runner.customer_orders
  ON runner_orders.order_id = customer_orders.order_id
WHERE pickup_time <> 'null'
GROUP BY runner_id
ORDER BY runner_id;
```
| runner_id | avg_time_to_pickup_min |
|-----------|------------------------|
| 1         | 15.67778               |
| 2         | 23.72                  |
| 3         | 10.46667               |

> 3. Is there any relationship between the number of pizzas and how long the order takes to prepare?
```sql
-- I have to assume that `pickup_time` is when the order was prepared.
-- Use a subquery to aggregate & pull in # of pizzas per order from `customer_order` table

SELECT 
  runner_orders.order_id, 
  subquery.pizza_count,
  (EXTRACT(EPOCH FROM pickup_time::TIMESTAMP)
      - EXTRACT(EPOCH FROM order_time)) / (60) AS order_prep_time
FROM 
 (SELECT
    order_id, order_time, COUNT(*) AS pizza_count
    FROM pizza_runner.customer_orders
    GROUP BY order_id, order_time
 ) subquery
INNER JOIN pizza_runner.runner_orders
  ON runner_orders.order_id = subquery.order_id
WHERE runner_orders.pickup_time <> 'null'
ORDER BY order_prep_time;
```
| order_id | pizza_count | order_prep_time |
|----------|-------------|-----------------|
| 2        | 1           | 10.03333        |
| 7        | 1           | 10.26667        |
| 5        | 1           | 10.46667        |
| 1        | 1           | 10.53333        |
| 10       | 2           | 15.51667        |
| 8        | 1           | 20.48333        |
| 3        | 2           | 21.23333        |
| 4        | 3           | 29.28333        |

Generally speaking, yes, the more pizzas ordered, the longer it takes to prepare the order. The outlier is `order_id` = 8, where it took nearly twice as long to prepare one pizza than would be expected given the data.

> 4. What was the average distance travelled for each customer?
```sql
-- `distance` is a string with various entries
-- Use `REGEXP_MATCH` to isolate the numbers, cannot `CAST` at the same time,
-- so need to do this in a CTE
WITH distance_fix AS (
  SELECT
    order_id,
    UNNEST(REGEXP_MATCH(distance, '[0-9]*\.*[0-9]*')) AS flat_distance
  FROM pizza_runner.runner_orders
  WHERE distance <> 'null'
)
-- Can average the distances now after casting as `NUMERIC`
SELECT
  customer_id,
  AVG(flat_distance::NUMERIC) AS avg_distance
-- Collapse `order_id` values in this subquery to prevent duplicate
-- rows (and incorrect averages) after joining to get `customer_id`
FROM (
  SELECT order_id, customer_id
  FROM pizza_runner.customer_orders
  GROUP BY order_id, customer_id
) subquery
INNER JOIN distance_fix
  ON distance_fix.order_id = subquery.order_id
GROUP BY customer_id
ORDER BY customer_id;
```
| customer_id | avg_distance        |
|-------------|---------------------|
| 101         | 20.0000000000000000 |
| 102         | 18.4000000000000000 |
| 103         | 23.4000000000000000 |
| 104         | 10.0000000000000000 |
| 105         | 25.0000000000000000 |

> 5. What was the difference between the longest and shortest delivery times for all orders?
```sql
-- This refers to the `duration` field, but this field is a string.
-- Use `REGEXP_MATCH` to isolate the numbers, cannot `CAST` at the same time,
-- so need to do this in a CTE
WITH duration_fix AS (
  SELECT
    order_id,
    UNNEST(REGEXP_MATCH(duration, '[0-9]*')) AS flat_duration
  FROM pizza_runner.runner_orders
  WHERE distance <> 'null'
)
-- Can calculate the difference now after casting as `NUMERIC`
SELECT
  MAX(flat_duration) AS max_duration,
  MIN(flat_duration) AS min_duration,
  MAX(flat_duration::NUMERIC) - MIN(flat_duration::NUMERIC) AS duration_diff
FROM duration_fix;
```
| max_duration | min_duration | duration_diff |
|--------------|--------------|---------------|
| 40           | 10           | 30            |

> 6. What was the average speed for each runner for each delivery and do you notice any trend for these values?
```sql
-- Use `REGEXP_MATCH` to isolate the numbers, cannot `CAST` at the same time,
-- so need to do this in a CTE
WITH dist_duration_fix AS (
  SELECT
    order_id,
    runner_id,
    UNNEST(REGEXP_MATCH(distance, '[0-9]*\.*[0-9]*')) AS flat_distance,
    UNNEST(REGEXP_MATCH(duration, '[0-9]*')) AS flat_duration
  FROM pizza_runner.runner_orders
  WHERE distance <> 'null' AND duration <> 'null'
)
-- Can calculate now after casting as `NUMERIC`
SELECT *,
  flat_distance::NUMERIC / flat_duration::NUMERIC AS speed_km_min,
  AVG(flat_distance::NUMERIC / flat_duration::NUMERIC) OVER ( 
    PARTITION BY runner_id -- Used a window function to see all info together in one table
  ) AS avg_runner_speed
FROM dist_duration_fix
ORDER BY runner_id;
```
| order_id | runner_id | flat_distance | flat_duration | speed_km_min           | avg_runner_speed       |
|----------|-----------|---------------|---------------|------------------------|------------------------|
| 1        | 1         | 20            | 32            | 0.62500000000000000000 | 0.75893518518518518519 |
| 2        | 1         | 20            | 27            | 0.74074074074074074074 | 0.75893518518518518519 |
| 3        | 1         | 13.4          | 20            | 0.67000000000000000000 | 0.75893518518518518519 |
| 10       | 1         | 10            | 10            | 1.00000000000000000000 | 0.75893518518518518519 |
| 7        | 2         | 25            | 25            | 1.00000000000000000000 | 1.04833333333333333333 |
| 8        | 2         | 23.4          | 15            | 1.5600000000000000     | 1.04833333333333333333 |
| 4        | 2         | 23.4          | 40            | 0.58500000000000000000 | 1.04833333333333333333 |
| 5        | 3         | 10            | 15            | 0.66666666666666666667 | 0.66666666666666666667 |

`runner_id` = 2 wins.

> 7. What is the successful delivery percentage for each runner?
