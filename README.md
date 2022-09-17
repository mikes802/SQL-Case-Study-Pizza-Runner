# 8-Week SQL Challenge: Case Study #2 - Pizza Runner

This is Case Study #2 from the 8-Week SQL Challenge of [Danny Ma's Serious SQL course](https://www.datawithdanny.com/ "Data With Danny"). My SQL [exploration](#data-exploration) and [solutions](#sql-query-solutions) can be found after the [background](#background) section below.

## Table of Contents
1. [Background](#background)
2. [Data Exploration](#data-exploration)
3. [SQL Query Solutions](#sql-query-solutions)

## [Background](#table-of-contents)
### Problem Statement


## [Data Exploration](#table-of-contents)
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