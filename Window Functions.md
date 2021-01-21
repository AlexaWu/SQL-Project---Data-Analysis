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

- Select the id, account_id, and total variable from the orders table, then create a column called total_rank that ranks this total amount of paper ordered (from highest to lowest) for each account using a partition. Your final table should have these four columns.
