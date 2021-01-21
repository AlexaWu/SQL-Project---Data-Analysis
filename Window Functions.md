# OVER, PARTITION BY, ORDER BY

Unlike regular aggregate functions, use of a window function does not cause rows to become grouped into a single output row — the rows retain their separate identities

Note: You can’t use window functions and standard aggregations in the same query. More specifically, you can’t include window functions in a GROUP BY clause.

- Create a running total of **standard_amt_usd** (in the `orders` table) over order time with no date truncation. Your final table should have two columns: one with the amount being added for each new row, and a second with the running total.
```javascript
SELECT standard_amt_usd,
       SUM(standard_amt_usd) OVER (ORDER BY occurred_at) AS running_total
FROM orders
```

- Create a running total of **standard_amt_usd** (in the `orders` table) over order time, but this time, date truncate **occurred_at** by year and partition by that same year-truncated **occurred_at** variable. Your final table should have three columns: One with the amount being added for each row, one for the truncated date, and a final column with the running total within each year.
```javascript
SELECT standard_amt_usd,
       DATE_TRUNC('year', occurred_at) as year,
       SUM(standard_amt_usd) OVER (PARTITION BY DATE_TRUNC('year', occurred_at) ORDER BY occurred_at) AS running_total
FROM orders
```

- Oracle “Partition By” example
```javascript
SELECT empno, deptno, COUNT(*) 
OVER (PARTITION BY deptno) DEPT_COUNT
FROM emp
```
The PARTITION BY clause sets the range of records that will be used for each "GROUP" within the OVER clause.
