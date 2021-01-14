# SQL Project - Parch & Posey
Window Functions, CTE, Advanced Joins &amp; Performance Tuning, Data Cleaning, Subqueries, Temporary Tables, Aggregations

### Parch & Posey Database: 
[DB Download](https://video.udacity-data.com/topher/2020/May/5eb5533b_parch-and-posey/parch-and-posey.sql)\
Parch and Posey, a hypothetical paper company's sales data of different types of paper (regular, poster, and glossy). Their clients are primarily large Fortune 100 companies whom they attract by advertising on Google, Facebook, and Twitter. The database consists of different [tables](https://github.com/AlexaWu/SQL-Project---Parch-Posey/tree/main/database%20in%20excel) linked with a database schema.

>SQL databases: [Postgres](https://www.postgresql.org/) ([online workspace](https://classroom.udacity.com/courses/ud198/lessons/614cf95a-13bf-406c-b092-e757178e633b/concepts/5a16ac08-fec2-475e-98d2-510611301aaf) / [local install guidance](https://medium.com/@gauravinthevalley/run-the-parch-posey-db-locally-in-postgres-8a0c2fde0c2e))

### Entity Relationship Diagrams(ERD)
![](https://video.udacity-data.com/topher/2017/November/5a0e2796_screen-shot-2017-11-16-at-3.54.06-pm/screen-shot-2017-11-16-at-3.54.06-pm.png)

### SQL Aggregations
SUM, AVG, COUNT, HAVING, [CASE and DATE functions](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Aggregations.md)

### [SQL Subqueries & Temporary Tables](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Subqueries%20%26%20Temporary%20Tables.md)
1. Find the average number of events for each day for each channel
2. What was the month/year combo for the first order placed? Find only the orders that took place in the same month/year as the first order, then pull the average for each type of paper qty in this month, and the total amount spent on all orders (in terms of usd).
3. Provide the name of the sales_rep in each region with the largest amount of total_amt_usd sales.
4. For the region with the largest (sum) of sales total_amt_usd, how many total (count) orders were placed?
5. How many accounts had more total purchases than the account name which has bought the most standard_qty paper throughout their lifetime as a customer?
6. For the customer that spent the most (in total over their lifetime as a customer) total_amt_usd, how many web_events did they have for each channel?
7. What is the lifetime average amount spent in terms of total_amt_usd for the top 10 total spending accounts?
8. What is the lifetime average amount spent in terms of total_amt_usd, including only the companies that spent more per order, on average, than the average of all orders.
