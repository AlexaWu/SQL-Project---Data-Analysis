# Subquery

- Find the **average number of events** for **each day** for **each channel**. The first table provide the number of events for each day and channel, and then average these values together using a second query

Step 1, group by the day and channel, then ordering by the number of events (the third column).

```sql
SELECT DATE_TRUNC('day',occurred_at) AS day,
	channel, COUNT(*) as events
FROM web_events
GROUP BY 1,2
ORDER BY 3 DESC;
```

Step 2, get a table that shows the **average** number of events a day for each channel.

```sql
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

---

- What was the month/year combo for the first order placed? Find only the orders that took place in the same month/year as the first order, then pull the average for each type of paper `qty` in this month, and the total amount spent on all orders (in terms of usd).

Step 1, pull the first month/year combo from the orders table

```sql
SELECT DATE_TRUNC('month', MIN(occurred_at)) 
FROM orders;
```
Step 2, get a table that shows the **average** number of events a day for each channel.

```sql
SELECT AVG(standard_qty) avg_std, AVG(gloss_qty) avg_gls, AVG(poster_qty) avg_pst, SUM(total_amt_usd)
FROM orders
WHERE DATE_TRUNC('month', occurred_at) = 
     (SELECT DATE_TRUNC('month', MIN(occurred_at)) 
     FROM orders);
```

---

- Provide the **name** of the **sales_rep** in **each region** with the largest amount of **total_amt_usd** sales.

First, I wanted to find the **total_amt_usd** totals associated with each **sales rep**, and I also wanted the region in which they were located. The query below provided this information.
```sql
SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
FROM sales_reps s
JOIN accounts a ON a.sales_rep_id = s.id
JOIN orders o ON o.account_id = a.id
JOIN region r ON r.id = s.region_id
GROUP BY 1,2
ORDER BY 3 DESC;
```
Next, I pulled the max for each region, and then we can use this to pull those rows in our final result.
```sql
SELECT region_name, MAX(total_amt) total_amt
     FROM(SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
             FROM sales_reps s
             JOIN accounts a ON a.sales_rep_id = s.id
             JOIN orders o ON o.account_id = a.id
             JOIN region r ON r.id = s.region_id
             GROUP BY 1, 2) t1
     GROUP BY 1;
```
Essentially, this is a **JOIN** of these two tables, where the region and amount match.
```sql
SELECT t3.rep_name, t3.region_name, t3.total_amt
FROM(SELECT region_name, MAX(total_amt) total_amt
     FROM(SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
             FROM sales_reps s
             JOIN accounts a ON a.sales_rep_id = s.id
             JOIN orders o ON o.account_id = a.id
             JOIN region r ON r.id = s.region_id
             GROUP BY 1, 2) t1
     GROUP BY 1) t2
JOIN (SELECT s.name rep_name, r.name region_name, SUM(o.total_amt_usd) total_amt
     FROM sales_reps s
     JOIN accounts a ON a.sales_rep_id = s.id
     JOIN orders o ON o.account_id = a.id
     JOIN region r ON r.id = s.region_id
     GROUP BY 1,2
     ORDER BY 3 DESC) t3
ON t3.region_name = t2.region_name 
AND t3.total_amt = t2.total_amt;
```

---

- For the region with the largest (sum) of sales **total_amt_usd**, how many **total** (count) orders were placed?

The first query I wrote was to pull the **total_amt_usd** for each **region**.
```sql
SELECT r.name region_name, SUM(o.total_amt_usd) total_amt
FROM sales_reps s 
JOIN accounts a ON a.sales_rep_id = s.id
JOIN orders o ON o.account_id = a.id
JOIN region r ON r.id = s.region_id
GROUP BY r.name;
```
Then we just want the region with the max amount from this table. There are two ways I considered getting this amount. One was to pull the max using a subquery. Another way is to order descending and just pull the top value.
```sql
SELECT MAX(total_amt)
FROM (SELECT r.name region_name, SUM(o.total_amt_usd) total_amt
             FROM sales_reps s
             JOIN accounts a ON a.sales_rep_id = s.id
             JOIN orders o ON o.account_id = a.id
             JOIN region r ON r.id = s.region_id
             GROUP BY r.name) sub;
```
Finally, we want to pull the total orders for the region with this amount:
```sql
SELECT r.name, COUNT(o.total) total_orders
FROM sales_reps s
JOIN accounts a ON a.sales_rep_id = s.id
JOIN orders o ON o.account_id = a.id
JOIN region r ON r.id = s.region_id
GROUP BY r.name
HAVING SUM(o.total_amt_usd) = (
      SELECT MAX(total_amt)
      FROM (SELECT r.name region_name, SUM(o.total_amt_usd) total_amt
              FROM sales_reps s
              JOIN accounts a ON a.sales_rep_id = s.id
              JOIN orders o ON o.account_id = a.id
              JOIN region r ON r.id = s.region_id
              GROUP BY r.name) sub);
```
>This provides the **Northeast** with **2357** orders.

---

- **How many accounts** had more **total** purchases than the account **name** which has bought the most **standard_qty** paper throughout their lifetime as a customer?

First, we want to find the account that had the most **standard_qty** paper. The query here pulls that account, as well as the total amount:
```javascript
SELECT a.name account_name, SUM(o.standard_qty) total_std, SUM(o.total) total
FROM accounts a
JOIN orders o ON o.account_id = a.id
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```
Now, I want to use this to pull all the accounts with more total sales:
```javascript
SELECT a.name
FROM orders o
JOIN accounts a ON a.id = o.account_id
GROUP BY 1
HAVING SUM(o.total) > (SELECT total 
                   FROM (SELECT a.name act_name, SUM(o.standard_qty) tot_std, SUM(o.total) total
                         FROM accounts a
                         JOIN orders o ON o.account_id = a.id
                         GROUP BY 1
                         ORDER BY 2 DESC
                         LIMIT 1) sub);
```
This is now a list of all the accounts with more total orders. We can get the count with just another simple subquery.
```javascript
SELECT COUNT(*)
FROM (SELECT a.name
       FROM orders o
       JOIN accounts a ON a.id = o.account_id
       GROUP BY 1
       HAVING SUM(o.total) > (SELECT total 
                   FROM (SELECT a.name act_name, SUM(o.standard_qty) tot_std, SUM(o.total) total
                         FROM accounts a
                         JOIN orders o ON o.account_id = a.id
                         GROUP BY 1
                         ORDER BY 2 DESC
                         LIMIT 1) inner_tab)
             ) counter_tab;
```
---
	     
- For the customer that spent the most (in total over their lifetime as a customer) **total_amt_usd**, how many **web_events** did they have for each channel?

Here, we first want to pull the customer with the most spent in lifetime value.
```javascript
SELECT a.id, a.name, SUM(o.total_amt_usd) tot_spent
FROM orders o
JOIN accounts a
ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY 3 DESC
LIMIT 1;
```
Now, we want to look at the number of events on each channel this company had, which we can match with just the **id**.
```javascript
SELECT a.name, w.channel, COUNT(*)
FROM accounts a
JOIN web_events w
ON a.id = w.account_id AND a.id =  (SELECT id
                     FROM (SELECT a.id, a.name, SUM(o.total_amt_usd) tot_spent
                           FROM orders o
                           JOIN accounts a
                           ON a.id = o.account_id
                           GROUP BY a.id, a.name
                           ORDER BY 3 DESC
                           LIMIT 1) inner_table)
GROUP BY 1, 2
ORDER BY 3 DESC;
```
I added an **ORDER BY** for no real reason, and the account name to assure I was only pulling from one account.

---
	
- What is the lifetime average amount spent in terms of **total_amt_usd** for the top 10 total spending **accounts**?

First, we just want to find the top 10 accounts in terms of highest **total_amt_usd**.
```javascript
SELECT a.id, a.name, SUM(o.total_amt_usd) tot_spent
FROM orders o
JOIN accounts a
ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY 3 DESC
LIMIT 10;
```
Now, we just want the average of these 10 amounts.
```javascript
SELECT AVG(tot_spent)
FROM (SELECT a.id, a.name, SUM(o.total_amt_usd) tot_spent
      FROM orders o
      JOIN accounts a
      ON a.id = o.account_id
      GROUP BY a.id, a.name
      ORDER BY 3 DESC
       LIMIT 10) temp;
```

---

- What is the lifetime average amount spent in terms of **total_amt_usd**, including only the companies that spent more per order, on average, than the average of all orders.

First, we want to pull the average of all accounts in terms of **total_amt_usd**:
```javascript
SELECT AVG(o.total_amt_usd) avg_all
FROM orders o
```
Then, we want to only pull the accounts with more than this average amount.
```javascript
SELECT o.account_id, AVG(o.total_amt_usd)
FROM orders o
GROUP BY 1
HAVING AVG(o.total_amt_usd) > (SELECT AVG(o.total_amt_usd) avg_all
                               FROM orders o);
```
Finally, we just want the average of these values.
```javascript
SELECT AVG(avg_amt)
FROM (SELECT o.account_id, AVG(o.total_amt_usd) avg_amt
    FROM orders o
    GROUP BY 1
    HAVING AVG(o.total_amt_usd) > (SELECT AVG(o.total_amt_usd) avg_all
                                   FROM orders o)) temp_table;
```


