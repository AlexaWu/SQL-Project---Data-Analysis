### Subquery

- Find the **average number of events** for **each day** for **each channel**. The first table provide the number of events for each day and channel, and then average these values together using a second query

Step 1, group by the day and channel, then ordering by the number of events (the third column).

```javascript
SELECT DATE_TRUNC('day',occurred_at) AS day,
	channel, COUNT(*) as events
FROM web_events
GROUP BY 1,2
ORDER BY 3 DESC;
```

Step 2, get a table that shows the **average** number of events a day for each channel.

```javascript
SELECT channel, AVG(events) AS average_events
FROM (SELECT DATE_TRUNC('day',occurred_at) AS day,
             channel, COUNT(*) as events
      FROM web_events 
      GROUP BY 1,2) sub
GROUP BY channel
ORDER BY 2 DESC;
```

> In the first subquery you wrote, you created a table that you could then query again in the **FROM** statement. \
However, if you are only returning a single value, you might use that value in a logical statement like **WHERE**, **HAVING**, or even **SELECT** - the value could be nested within a **CASE** statement. Note that you should not include an alias when you write a subquery in a conditional statement. This is because the subquery is treated as an individual value (or set of values in the IN case) rather than as a table.\
Also, notice the query here compared a single value. If we returned an entire column **IN** would need to be used to perform a logical argument. If we are returning an entire table, then we must use an **ALIAS** for the table, and perform additional logic on the entire table.

- What was the month/year combo for the first order placed? Find only the orders that took place in the same month/year as the first order, then pull the average for each type of paper `qty` in this month, and the total amount spent on all orders (in terms of usd).

Step 1, pull the first month/year combo from the orders table

```javascript
SELECT DATE_TRUNC('month', MIN(occurred_at)) 
FROM orders;
```
Step 2, get a table that shows the **average** number of events a day for each channel.

```javascript
SELECT AVG(standard_qty) avg_std, AVG(gloss_qty) avg_gls, AVG(poster_qty) avg_pst, SUM(total_amt_usd)
FROM orders
WHERE DATE_TRUNC('month', occurred_at) = 
     (SELECT DATE_TRUNC('month', MIN(occurred_at)) FROM orders);
```

- Provide the **name** of the **sales_rep** in **each region** with the largest amount of **total_amt_usd** sales.

First, I wanted to find the **total_amt_usd** totals associated with each **sales rep**, and I also wanted the region in which they were located. The query below provided this information.
```javascript
SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
FROM sales_reps s
JOIN accounts a
ON a.sales_rep_id = s.id
JOIN orders o
ON o.account_id = a.id
JOIN region r
ON r.id = s.region_id
GROUP BY 1,2
ORDER BY 3 DESC;
```
Next, I pulled the max for each region, and then we can use this to pull those rows in our final result.
```javascript
SELECT region_name, MAX(total_amt) total_amt
     FROM(SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
             FROM sales_reps s
             JOIN accounts a
             ON a.sales_rep_id = s.id
             JOIN orders o
             ON o.account_id = a.id
             JOIN region r
             ON r.id = s.region_id
             GROUP BY 1, 2) t1
     GROUP BY 1;
```
Essentially, this is a **JOIN** of these two tables, where the region and amount match.
```javascript
SELECT t3.rep_name, t3.region_name, t3.total_amt
FROM(SELECT region_name, MAX(total_amt) total_amt
     FROM(SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
             FROM sales_reps s
             JOIN accounts a
             ON a.sales_rep_id = s.id
             JOIN orders o
             ON o.account_id = a.id
             JOIN region r
             ON r.id = s.region_id
             GROUP BY 1, 2) t1
     GROUP BY 1) t2
JOIN (SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
     FROM sales_reps s
     JOIN accounts a
     ON a.sales_rep_id = s.id
     JOIN orders o
     ON o.account_id = a.id
     JOIN region r
     ON r.id = s.region_id
     GROUP BY 1,2
     ORDER BY 3 DESC) t3
ON t3.region_name = t2.region_name AND t3.total_amt = t2.total_amt;
```

- For the region with the largest (sum) of sales total_amt_usd, how many total (count) orders were placed?


How many accounts had more total purchases than the account name which has bought the most standard_qty paper throughout their lifetime as a customer?


For the customer that spent the most (in total over their lifetime as a customer) total_amt_usd, how many web_events did they have for each channel?


What is the lifetime average amount spent in terms of total_amt_usd for the top 10 total spending accounts?


What is the lifetime average amount spent in terms of total_amt_usd, including only the companies that spent more per order, on average, than the average of all orders.
