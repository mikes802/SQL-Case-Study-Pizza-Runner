# 8-Week SQL Challenge: Case Study #2 - Pizza Runner

This is Case Study #2 from the 8-Week SQL Challenge of [Danny Ma's Serious SQL course](https://www.datawithdanny.com/ "Data With Danny"). My SQL [exploration](#data-exploration) and [solutions](#sql-query-solutions) can be found after the [background](#background) section below.

## Table of Contents
1. [Background](#background)
2. [Data Exploration](#data-exploration)
3. [SQL Query Solutions](#sql-query-solutions)

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
### Pizza Metrics
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
WHERE cancellation != 'Restaurant Cancellation'
  AND cancellation != 'Customer Cancellation'
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
  WHERE cancellation != 'Restaurant Cancellation'
    AND cancellation != 'Customer Cancellation'
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
  WHERE cancellation != 'Restaurant Cancellation'
    AND cancellation != 'Customer Cancellation'
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
-- Successful delivery CTE
WITH successful_deliveries AS (
  SELECT
    order_id
  FROM pizza_runner.runner_orders
  WHERE cancellation != 'Restaurant Cancellation'
    AND cancellation != 'Customer Cancellation'
    OR cancellation IS NULL -- Need to include or will not return these rows
)
SELECT
  customer_id,
  SUM(
    CASE WHEN (exclusions != '' AND exclusions != 'null' AND exclusions IS NOT NULL)
           OR (extras != '' AND extras != 'null' AND extras IS NOT NULL)  -- I'm looking for entries
         THEN 1
         ELSE 0
    END
  ) AS order_change_count,
  SUM(
    CASE WHEN (exclusions = '' OR exclusions = 'null' OR exclusions IS NULL)
          AND (extras = '' OR extras = 'null' OR extras IS NULL)  -- I'm looking for no entries
         THEN 1
         ELSE 0
    END
  ) AS no_order_change_count
FROM successful_deliveries
INNER JOIN pizza_runner.customer_orders -- There are multiple rows with same id
  ON successful_deliveries.order_id = customer_orders.order_id
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
WITH successful_deliveries AS (
  SELECT
    order_id
  FROM pizza_runner.runner_orders
  WHERE cancellation != 'Restaurant Cancellation'
    AND cancellation != 'Customer Cancellation'
    OR cancellation IS NULL -- Need to include or will not return these rows
)
SELECT
  SUM(
    CASE WHEN (exclusions != '' AND exclusions != 'null' AND exclusions IS NOT NULL)
          AND (extras != '' AND extras != 'null' AND extras IS NOT NULL)  -- I'm looking for numbers in both
        THEN 1
        ELSE 0
    END
  ) AS order_change_count
FROM successful_deliveries
INNER JOIN pizza_runner.customer_orders -- There are multiple rows with same id
  ON successful_deliveries.order_id = customer_orders.order_id;
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
GROUP BY day_of_week
ORDER BY order_count DESC;
```
| day_of_week | order_count |
|-------------|-------------|
| Monday      | 5           |
| Friday      | 5           |
| Saturday    | 3           |
| Sunday      | 1           |
