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
