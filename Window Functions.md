# OVER, PARTITION BY, ORDER BY

> Note: can’t use window functions and standard aggregations in the same query. More specifically, can’t include window functions in a GROUP BY clause.

- Create a running total of **standard_amt_usd** (in the `orders` table) over order time with no date truncation. Final table should have two columns: the amount being added for each new row, and the running total.
```javascript
SELECT standard_amt_usd,
       SUM(standard_amt_usd) OVER (ORDER BY occurred_at) AS running_total
FROM orders
```

- Create a running total of **standard_amt_usd** (in the `orders` table) over order time, but this time, date truncate **occurred_at** by year and partition by that same year-truncated **occurred_at** variable. Final table should have three columns: the amount being added for each row, the truncated date, and the running total within each year.
```javascript
SELECT standard_amt_usd,
       DATE_TRUNC('year', occurred_at) as year,
       SUM(standard_amt_usd) OVER (PARTITION BY DATE_TRUNC('year', occurred_at) ORDER BY occurred_at) AS running_total
FROM orders
```

- [Oracle “Partition By” example](https://stackoverflow.com/questions/561836/oracle-partition-by-keyword)
```javascript
SELECT empno, deptno, COUNT(*) 
OVER (PARTITION BY deptno) DEPT_COUNT
FROM emp
```
The PARTITION BY clause sets the range of records that will be used for each "GROUP" within the OVER clause.
