# FULL OUTER JOIN

> FULL JOIN is commonly used in conjunction with aggregations to understand the amount of overlap between two tables. A common application of this is when joining two tables on a timestamp. 

you're an analyst at Parch & Posey and you want to see:

- `each account who has a sales rep` and `each sales rep that has an account` (all of the columns in these returned rows will be full)
- but also `each account that does not have a sales rep` and `each sales rep that does not have an account` (some of the columns in these returned rows will be empty)

so, selecting all of the columns in both of the relevant tables, accounts and sales_reps

```javascript
SELECT *
FROM accounts
FULL JOIN sales_reps ON accounts.sales_rep_id = sales_reps.id
```

# Inequality JOINs

> Inequality operators (a.k.a. comparison operators) don't only need to be date times or numbers, they also work on strings

- write a query that **left joins** the `accounts` table and the `sales_reps` tables on each **sale rep's ID number** and joins it using the **<** comparison operator on **accounts.primary_poc** and **sales_reps.name**. The query results should be a table with three columns: the `account name` (e.g. Johnson Controls), the `primary contact name` (e.g. Cammy Sosnowski), and the `sales representative's name` (e.g. Samuel Racine)

```javascript
SELECT accounts.name as account_name,
       accounts.primary_poc as poc_name,
       sales_reps.name as sales_rep_name
FROM accounts
LEFT JOIN sales_reps
ON accounts.sales_rep_id = sales_reps.id
AND accounts.primary_poc < sales_reps.name
 ```
 
 >What is the relationship between accounts.primary_poc and sales_reps.name?\
 The primary point of contact's full name comes before the sales representative's name alphabetically
 
 # Self JOINs
 
 >One of the most common use cases for self JOINs is in cases where two events occurred, one after another.

[Date/Time Functions and Operators](https://www.postgresql.org/docs/8.2/functions-datetime.html)

- find those web events that occurred after, but not more than 1 day after, another web event, the result includes a column for the channel variable in both instances of the table 

```javascript
SELECT we1.id AS we_id,
       we1.account_id AS we1_account_id,
       we1.occurred_at AS we1_occurred_at,
       we1.channel AS we1_channel,
       we2.id AS we2_id,
       we2.account_id AS we2_account_id,
       we2.occurred_at AS we2_occurred_at,
       we2.channel AS we2_channel
  FROM web_events we1 
 LEFT JOIN web_events we2
   ON we1.account_id = we2.account_id
  AND we1.occurred_at > we2.occurred_at
  AND we1.occurred_at <= we2.occurred_at + INTERVAL '1 day'
ORDER BY we1.account_id, we2.occurred_at
```
