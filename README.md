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
