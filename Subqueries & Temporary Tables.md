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

Step 2, get a table that shows the average number of events a day for each channel.

```javascript
SELECT channel, AVG(events) AS average_events
FROM (SELECT DATE_TRUNC('day',occurred_at) AS day,
             channel, COUNT(*) as events
      FROM web_events 
      GROUP BY 1,2) sub
GROUP BY channel
ORDER BY 2 DESC;

select channel, count(date_trunc),
	sum(count)/count(date_trunc) as avg
from
(select date_trunc('day', occurred_at), channel, count(*)
from web_events
group by date_trunc, channel
) as sub
group by channel;
```


