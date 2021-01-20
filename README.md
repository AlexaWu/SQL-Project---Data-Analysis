# SQL Project - Parch & Posey
Window Functions, CTE, Advanced Joins &amp; Performance Tuning, Data Cleaning, Subqueries, Temporary Tables, Aggregations

### Parch & Posey Database: 
[DB Download](https://video.udacity-data.com/topher/2020/May/5eb5533b_parch-and-posey/parch-and-posey.sql)\
Parch and Posey, a hypothetical paper company's sales data of different types of paper (regular, poster, and glossy). Their clients are primarily large Fortune 100 companies whom they attract by advertising on Google, Facebook, and Twitter. The database consists of different [tables](https://github.com/AlexaWu/SQL-Project---Parch-Posey/tree/main/database%20in%20excel) linked with a database schema.

>SQL databases: [Postgres](https://www.postgresql.org/) ([online workspace](https://classroom.udacity.com/courses/ud198/lessons/614cf95a-13bf-406c-b092-e757178e633b/concepts/5a16ac08-fec2-475e-98d2-510611301aaf) / [local install guidance](https://medium.com/@gauravinthevalley/run-the-parch-posey-db-locally-in-postgres-8a0c2fde0c2e))

### Entity Relationship Diagrams(ERD)
![](https://video.udacity-data.com/topher/2017/November/5a0e2796_screen-shot-2017-11-16-at-3.54.06-pm/screen-shot-2017-11-16-at-3.54.06-pm.png)

### SQL Aggregations
SUM, AVG, COUNT, HAVING

[DATE](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Date.md)
1. Find the sales in terms of total dollars for all orders in each year, ordered from greatest to least. Do you notice any trends in the yearly sales totals?
2. Which month did Parch & Posey have the greatest sales in terms of total dollars? Are all months evenly represented by the dataset?
3. Which year/month did Parch & Posey have the greatest sales in terms of total number of orders? Are all years/months evenly represented by the dataset?
4. In which month of which year did Walmart spend the most on gloss paper in terms of dollars?

[CASE](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Case.md)
1. Write a query to display for each order, the account ID, total amount of the order, and the level of the order - ‘Large’ or ’Small’ - depending on if the order is $3000 or more, or smaller than $3000.
2. Write a query to display the number of orders in each of three categories, based on the total number of items in each order. The three categories are: 'At Least 2000', 'Between 1000 and 2000' and 'Less than 1000'.
3. We would like to understand 3 different levels of customers based on the amount associated with their purchases. The top level includes anyone with a Lifetime Value (total sales of all orders) greater than 200,000 usd. The second level is between 200,000 and 100,000 usd. The lowest level is anyone under 100,000 usd. Provide a table that includes the level associated with each account. You should provide the account name, the total sales of all orders for the customer, and the level. Order with the top spending customers listed first.
4. We would now like to perform a similar calculation to the first, but we want to obtain the total amount spent by customers only in 2016 and 2017. Keep the same levels as in the previous question. Order with the top spending customers listed first.
5. We would like to identify top performing sales reps, which are sales reps associated with more than 200 orders. Create a table with the sales rep name, the total number of orders, and a column with top or not depending on if they have more than 200 orders. Place the top sales people first in your final table.
6. We would like to identify top performing sales reps, which are sales reps associated with more than 200 orders or more than 750000 in total sales. The middle group has any rep with more than 150 orders or 500000 in sales. Create a table with the sales rep name, the total number of orders, total sales across all orders, and a column with top, middle, or low depending on this criteria. Place the top sales people based on dollar amount of sales first in your final table.

### SQL Subqueries & Common Table Expression
[Subqueries](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Subqueries%20%26%20Temporary%20Tables.md)
1. Find the average number of events for each day for each channel
2. What was the month/year combo for the first order placed? Find only the orders that took place in the same month/year as the first order, then pull the average for each type of paper qty in this month, and the total amount spent on all orders (in terms of usd).
2. Provide the name of the sales_rep in each region with the largest amount of total_amt_usd sales.
4. For the region with the largest (sum) of sales total_amt_usd, how many total (count) orders were placed?
5. How many accounts had more total purchases than the account name which has bought the most standard_qty paper throughout their lifetime as a customer?
6. For the customer that spent the most (in total over their lifetime as a customer) total_amt_usd, how many web_events did they have for each channel?
7. What is the lifetime average amount spent in terms of total_amt_usd for the top 10 total spending accounts?
8. What is the lifetime average amount spent in terms of total_amt_usd, including only the companies that spent more per order, on average, than the average of all orders.

[Common Table Expression/WITH](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Common%20Table%20Expression.md)

Same as Subqueries 3-8, solve in CTE (more readable and efficient)

### SQL Data Cleaning
[LEFT, RIGHT, LENGTH](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Data%20Cleaning.md#left-right-length)
1. In the accounts table, there is a column holding the website for each company. The last three digits specify what type of web address they are using. A list of extensions (and pricing) is provided. Pull these extensions and provide how many of each website type exist in the accounts table.
2. There is much debate about how much the name (or even the first letter of a company name) matters. Use the accounts table to pull the first letter of each company name to see the distribution of company names that begin with each letter (or number).
3. Use the accounts table and a CASE statement to create two groups: one group of company names that start with a number and a second group of those company names that start with a letter. What proportion of company names start with a letter?
4. Consider vowels as a, e, i, o, and u. What proportion of company names start with a vowel, and what percent start with anything else?

[POSITION, STRPOS, & SUBSTR](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/LEFT,%20RIGHT,%20LENGTH.md#position-strpos--substr)
1. Use the accounts table to create first and last name columns that hold the first and last names for the primary_poc.
2. Now see if you can do the same thing for every rep name in the sales_reps table. Again provide first and last name columns.

[CONCAT, Piping ||](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Data%20Cleaning.md#concat-piping-)
1. Each company in the accounts table wants to create an email address for each primary_poc. The email address should be the first name of the primary_poc . last name primary_poc @ company name .com.
2. Create an email address that will work by removing all of the spaces in the account name.
3. We would also like to create an initial password, which they will change after their first log in. The first password will be the first letter of the primary_poc's first name (lowercase), then the last letter of their first name (lowercase), the first letter of their last name (lowercase), the last letter of their last name (lowercase), the number of letters in their first name, the number of letters in their last name, and then the name of the company they are working with, all capitalized with no spaces.

[TO_DATE, CAST, Casting with ::](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Data%20Cleaning.md#to_date-cast-casting-with-)
> work with SF Crime Data database ([online workspace](https://classroom.udacity.com/courses/ud198/lessons/03f64082-fa4d-4aff-80be-d48597867e07/concepts/a9de2023-ae43-4781-a5c5-050bf5c33dd9))
1. Write a query to look at the top 10 rows to understand the columns and the raw data in the dataset called sf_crime_data.
2. Write a query to change the date into the correct SQL date format. Using SUBSTR and CONCAT to perform this operation.
3. Once you have created a column in the correct format, use either CAST or :: to convert this to a date.

[COALESCE](https://github.com/AlexaWu/SQL-Project---Parch-Posey/blob/main/Data%20Cleaning.md#coalesce)
1. Use COALESCE to fill in the accounts.id column with the account.id for the NULL value
2. Use COALESCE to fill in the orders.account_id column with the account.id for the NULL value
3. Use COALESCE to fill in each of the qty and usd columns with 0

### [Advanced] SQL Window Functions

[OVER, PARTITION BY, ORDER BY]()
