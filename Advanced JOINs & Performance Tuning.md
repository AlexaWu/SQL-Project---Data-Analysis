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

Inequality operators (a.k.a. comparison operators) don't only need to be date times or numbers, they also work on strings

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
