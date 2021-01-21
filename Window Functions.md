# OVER, PARTITION BY, ORDER BY

> Note: \
Window functions can’t be used with standard aggregations in the same query. (can’t include window functions in a GROUP BY clause.)\
Window functions are permitted only in the SELECT list and the ORDER BY clause of the query.\
Window functions execute after regular aggregate functions

Each windowing behavior can be named in a WINDOW clause and then referenced in OVER. For example:
```javascript
SELECT sum(salary) OVER w, avg(salary) OVER w
  FROM empsalary
  WINDOW w AS (PARTITION BY depname ORDER BY salary DESC);
```

- Create a running total of **standard_amt_usd** (in the `orders` table) over order time with no date truncation. Final table should have two columns: the `amount being added for each new row`, and the `running total`.
```javascript
SELECT standard_amt_usd,
       SUM(standard_amt_usd) OVER (ORDER BY occurred_at) AS running_total
FROM orders
```

- Create a running total of **standard_amt_usd** (in the `orders` table) over order time, but this time, **date truncate occurred_at by year** and **partition by that same year-truncated** occurred_at variable. Final table should have three columns: the `amount being added for each row`, the `truncated date`, and the `running total within each year`.
```javascript
SELECT standard_amt_usd,
       DATE_TRUNC('year', occurred_at) as year,
       SUM(standard_amt_usd) OVER (PARTITION BY DATE_TRUNC('year', occurred_at) ORDER BY occurred_at) AS running_total
FROM orders
```

Reference:
[Window Function Calls](https://www.postgresql.org/docs/9.1/sql-expressions.html#SYNTAX-WINDOW-FUNCTIONS),
[General-Purpose Window Functions](https://www.postgresql.org/docs/9.1/functions-window.html#FUNCTIONS-WINDOW-TABLE),
[Window Function Processing](https://www.postgresql.org/docs/9.1/queries-table-expressions.html#QUERIES-WINDOW),
[SELECT](https://www.postgresql.org/docs/9.1/sql-select.html)

---

# ROW_NUMBER & RANK

- Select the **id**, **account_id**, and **total variable** from the `orders` table, then create a column called **total_rank** that ranks this total amount of paper ordered (from highest to lowest) for each account using a partition. Your final table should have these four columns.
```javascript
SELECT id,
       account_id,
       total,
       RANK() OVER (PARTITION BY account_id ORDER BY total DESC) AS total_rank
FROM orders
```

# Aggregates in Window Functions
```javascript
SELECT id,
       account_id,
       standard_qty,
       DATE_TRUNC('month', occurred_at) AS month,
       DENSE_RANK() OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS dense_rank,
       SUM(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS sum_std_qty,
       COUNT(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS count_std_qty,
       AVG(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS avg_std_qty,
       MIN(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS min_std_qty,
       MAX(standard_qty) OVER (PARTITION BY account_id ORDER BY DATE_TRUNC('month',occurred_at)) AS max_std_qty
FROM orders
```

# Aliases for Multiple Window Functions
```javascript
SELECT id,
       account_id,
       DATE_TRUNC('year',occurred_at) AS year,
       DENSE_RANK() OVER account_year_window AS dense_rank,
       total_amt_usd,
       SUM(total_amt_usd) OVER account_year_window AS sum_total_amt_usd,
       COUNT(total_amt_usd) OVER account_year_window AS count_total_amt_usd,
       AVG(total_amt_usd) OVER account_year_window AS avg_total_amt_usd,
       MIN(total_amt_usd) OVER account_year_window AS min_total_amt_usd,
       MAX(total_amt_usd) OVER account_year_window AS max_total_amt_usd
FROM orders 
WINDOW account_year_window AS (PARTITION BY account_id ORDER BY DATE_TRUNC('year',occurred_at))
```
# Comparing a Row to Previous Row

LAG function: Return the value from a previous row to the current row in the table.\
LEAD function: Return the value from the row following the current row in the table.
```javascript
SELECT occurred_at,
       total_amt_usd,
       LEAD(total_amt_usd) OVER (ORDER BY occurred_at) AS lead,
       LEAD(total_amt_usd) OVER (ORDER BY occurred_at) - total_amt_usd AS lead_difference
FROM (
SELECT occurred_at,
       SUM(total_amt_usd) AS total_amt_usd
  FROM orders 
 GROUP BY 1
) sub
```
