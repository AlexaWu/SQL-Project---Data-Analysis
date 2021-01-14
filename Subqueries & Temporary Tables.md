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

- What was the month/year combo for the first order placed? Find only the orders that took place in the same month/year as the first order, then pull the average for each type of paper `qty` in this month.
