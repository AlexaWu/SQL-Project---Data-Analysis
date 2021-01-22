# DATEs

- Find the sales in terms of **total dollars for all orders** in **each year**, ordered from greatest to least.

```javascript
SELECT DATE_PART('year', occurred_at) ord_year,  SUM(total_amt_usd) total_spent
FROM orders
GROUP BY 1
ORDER BY 2 DESC;
```
> When we look at the yearly totals, 2013 and 2017 have much smaller totals than all other years. If we look further at the monthly data, we see that for 2013 and 2017 there is only one month of sales for each of these years (12 for 2013 and 1 for 2017). Therefore, neither of these are evenly represented. Sales have been increasing year over year, with 2016 being the largest sales to date. At this rate, we might expect 2017 to have the largest sales.

- Which month did Parch & Posey have the **greatest sales in terms of total dollars**? Are all months evenly represented by the dataset?

```javascript
SELECT DATE_PART('month', occurred_at) ord_month, SUM(total_amt_usd) total_spent
FROM orders
WHERE occurred_at BETWEEN '2014-01-01' AND '2017-01-01'
GROUP BY 1
ORDER BY 2 DESC; 
```
> In order for this to be 'fair', we should remove the sales from 2013 and 2017. The greatest sales amounts occur in December (12).

- Which **year** did Parch & Posey have the greatest sales in terms of **total number of orders**? Are all years evenly represented by the dataset?

```javascript
SELECT DATE_PART('year', occurred_at) ord_year,  COUNT(*) total_sales
FROM orders
GROUP BY 1
ORDER BY 2 DESC;
```
> Again, 2016 by far has the most amount of orders, but again 2013 and 2017 are not evenly represented to the other years in the dataset.

- Which **month** did Parch & Posey have the greatest sales in terms of **total number of orders**? Are all months evenly represented by the dataset?

```javascript
SELECT DATE_PART('month', occurred_at) ord_month, COUNT(*) total_sales
FROM orders
WHERE occurred_at BETWEEN '2014-01-01' AND '2017-01-01'
GROUP BY 1
ORDER BY 2 DESC; 
```
> December still has the most sales, but interestingly, November has the second most sales (but not the most dollar sales. To make a fair comparison from one month to another 2017 and 2013 data were removed.

- In which **month** of which year did **Walmart spend the most on gloss paper** in terms of dollars?

```javascript
SELECT DATE_TRUNC('month', o.occurred_at) ord_date, SUM(o.gloss_amt_usd) tot_spent
FROM orders o 
JOIN accounts a
ON a.id = o.account_id
WHERE a.name = 'Walmart'
GROUP BY 1
ORDER BY 2 DESC
LIMIT 1;
```
> May 2016 was when Walmart spent the most on gloss paper.

---

# CASE

- Write a query to display the **number of orders in each of three categories**, based on the **total number of items in each order**. The three categories are: 'At Least 2000', 'Between 1000 and 2000' and 'Less than 1000'.

```javascript
SELECT CASE WHEN total >= 2000 THEN 'At Least 2000'
   WHEN total >= 1000 AND total < 2000 THEN 'Between 1000 and 2000'
   ELSE 'Less than 1000' END AS order_category,
COUNT(*) AS order_count
FROM orders
GROUP BY 1;
```

- We would like to understand **3 different levels of customers** based on the **amount associated with their purchases**. The top level includes anyone with a Lifetime Value (total sales of all orders) `greater than 200,000` usd. The second level is `between 200,000 and 100,000` usd. The lowest level is anyone `under 100,000` usd. Provide a table that includes the **level associated with each account**. You should provide the **account name**, the **total sales of all orders** for the customer, and the **level**. Order with the top spending customers listed first.

```javascript
SELECT a.name, SUM(total_amt_usd) total_spent, 
     CASE WHEN SUM(total_amt_usd) > 200000 THEN 'top'
     WHEN  SUM(total_amt_usd) > 100000 THEN 'middle'
     ELSE 'low' END AS customer_level
FROM orders o
JOIN accounts a
ON o.account_id = a.id 
GROUP BY a.name
ORDER BY 2 DESC;
```

- We would now like to perform a similar calculation to the first, but we want to obtain the **total amount spent by customers only in `2016` and `2017`**. Keep the same **levels** as in the previous question. Order with the top spending customers listed first.

```javascript
SELECT a.name, SUM(total_amt_usd) total_spent, 
     CASE WHEN SUM(total_amt_usd) > 200000 THEN 'top'
     WHEN  SUM(total_amt_usd) > 100000 THEN 'middle'
     ELSE 'low' END AS customer_level
FROM orders o
JOIN accounts a
ON o.account_id = a.id
WHERE occurred_at > '2015-12-31' 
GROUP BY 1
ORDER BY 2 DESC;
```

- We would like to identify **top performing sales reps**, which are sales reps associated with `more than 200 orders`. Create a table with the **sales rep name**, the **total number of order**s, and **a column with `top` or `not`** depending on if they have more than 200 orders. Place the top sales people first in your final table.

```javascript
SELECT s.name, COUNT(*) num_ords,
     CASE WHEN COUNT(*) > 200 THEN 'top'
     ELSE 'not' END AS sales_rep_level
FROM orders o
JOIN accounts a
ON o.account_id = a.id 
JOIN sales_reps s
ON s.id = a.sales_rep_id
GROUP BY s.name
ORDER BY 2 DESC;
```
> this assumes each name is unique - which has been done a few times. We otherwise would want to break by the name and the id of the table.

- We would like to identify **top performing sales reps**, which are sales reps associated with `more than 200 orders or more than 750000 in total sales`. The middle group has any rep with `more than 150 orders or 500000 in sales`. Create a table with the **sales rep name**, the **total number of orders**, **total sales across all orders**, and **a column with `top`, `middle`, or `low`** depending on this criteria. Place the top sales people based on dollar amount of sales first in your final table.

```javascript
SELECT s.name, COUNT(*), SUM(o.total_amt_usd) total_spent, 
     CASE WHEN COUNT(*) > 200 OR SUM(o.total_amt_usd) > 750000 THEN 'top'
     WHEN COUNT(*) > 150 OR SUM(o.total_amt_usd) > 500000 THEN 'middle'
     ELSE 'low' END AS sales_rep_level
FROM orders o
JOIN accounts a
ON o.account_id = a.id 
JOIN sales_reps s
ON s.id = a.sales_rep_id
GROUP BY s.name
ORDER BY 3 DESC;
```


