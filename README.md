# Retail Sales Analysis – SQL Project


***Project Overview***
This project aims to analyze retail sales data using SQL Server. The dataset contains transactional details such as sales date, time, customer demographics, product category, quantity, and monetary metrics (price, cost, total sale). Through SQL queries, we explore, clean, and derive insights that can aid business decisions.

***Project Objectives***
- Load and manage retail sales data in SQL Server.
- Clean the dataset by handling missing values.
- Explore key performance indicators (KPIs) like total sales, customers, and categories.
- Perform data-driven analysis to answer business questions.
- Identify best-selling periods, customer behavior, and category performance.

***Project Structure***

**1. Database Setup:**
- Database: SQL_Project
- Table: retail_sales
- Columns: transaction_id, sale_date, sale_time, customer_id, gender, age, category, quantity, price_per_unit, cogs, total_sale
```sql
-- SQL Retail Sales Analysis --
CREATE DATABASE SQL_Project

-- Create a Table--
DROP TABLE IF EXISTS retail_sales;
CREATE TABLE retail_sales 
			(
				transactions_id INT PRIMARY KEY,
				sale_date DATE,
				sale_time TIME,
				customer_id INT,
				gender VARCHAR(255),
				age INT,
				category VARCHAR(255),
				quantiy INT,
				price_per_unit FLOAT,
				cogs FLOAT,
				total_sale FLOAT
			);
```

- Data loaded via BULK INSERT from a .csv file.
```sql
BULK INSERT [dbo].[retail_sales]
FROM "C:\Users\HP\Downloads\SQL - Retail Sales Analysis_utf .csv"
WITH (
    FORMAT = 'CSV',
    FIRSTROW = 2,
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    TABLOCK
);
```


**2. Data Cleaning & Exploration:**

- Null Value Check: Check for any null values in the dataset and delete records with missing data.
```sql
-------------------------
----- DATA Cleaning -----
-------------------------

-- Check for "NULL" Values
SELECT * FROM retail_sales 
WHERE transactions_id IS NULL OR sale_date IS NULL OR 
sale_time IS NULL OR customer_id IS NULL OR gender is NULL OR
age IS NULL OR category IS NULL OR quantiy IS NULL OR
price_per_unit IS NULL OR cogs IS NULL OR total_sale IS NULL

-- Delete "NULL" Values
DELETE FROM retail_sales
WHERE transactions_id IS NULL OR sale_date IS NULL OR
sale_time IS NULL OR customer_id IS NULL OR gender is NULL OR
age IS NULL OR category IS NULL OR quantiy IS NULL OR
price_per_unit IS NULL OR cogs IS NULL OR total_sale IS NULL
```
- Record Count: Determine the total number of records in the dataset.
- Customer Count: Find out how many unique customers are in the dataset.
- Category Count: Identify all unique product categories in the dataset.
```sql
----------------------------
----- DATA EXPLORATION -----
----------------------------
SELECT * FROM retail_sales

-- Total Sales Count
SELECT COUNT(*) AS Total_Sales FROM retail_sales	--1987

-- Total number of customers
SELECT COUNT(DISTINCT customer_id) AS Total_Customers FROM retail_sales		-- 155

-- Total no of category
SELECT COUNT(DISTINCT category) AS Total_Category FROM retail_sales	-- 3
```

**3. Data Analysis & Business Questions:**
```sql
-----------------------------------------------------------
----- Data Analysis & Business Key Problems & Answers -----
-----------------------------------------------------------
-- My Analysis & Findings

-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05

SELECT * FROM retail_sales
WHERE sale_date = '2022-11-05'



-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 2 in the month of Nov-2022

SELECT * FROM retail_sales
WHERE category = 'Clothing' AND
quantiy > 2 AND
FORMAT(sale_date, 'yyyy-MM') = '2022-11'



-- Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.

SELECT category, COUNT(*) AS Total_Orders, SUM(total_sale) AS Total_Sales
FROM retail_sales
GROUP BY category



-- Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.

SELECT category, AVG(age) Avg_Age FROM retail_sales
WHERE category = 'Beauty'
GROUP BY category



-- Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.

SELECT * FROM retail_sales
WHERE total_sale > 1000


-- Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.

SELECT category, gender, COUNT(*) Total_Txns FROM retail_sales
GROUP BY category, gender


-- Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year

SELECT Sales_Year, Sales_Month, Avg_Sale FROM (
SELECT FORMAT(sale_date, 'yyyy') Sales_Year,
FORMAT(sale_date, 'MM') Sales_Month,
ROUND(AVG(total_sale), 2) Avg_Sale,
RANK() OVER(PARTITION BY FORMAT(sale_date, 'yyyy') ORDER BY AVG(total_sale) Desc) Rnk
FROM retail_sales
GROUP BY FORMAT(sale_date, 'yyyy'), FORMAT(sale_date, 'MM')) AS a
WHERE Rnk = 1


-- With CTE
WITH Monthly_Avg_Sales AS (
SELECT 
FORMAT(sale_date, 'yyyy') AS Sales_Year,
FORMAT(sale_date, 'MM') AS Sales_Month,
ROUND(AVG(total_sale), 2) AS Avg_Sale,
RANK() OVER (PARTITION BY FORMAT(sale_date, 'yyyy') ORDER BY AVG(total_sale) DESC) AS Rnk
FROM retail_sales
GROUP BY FORMAT(sale_date, 'yyyy'), FORMAT(sale_date, 'MM'))


SELECT Sales_Year, Sales_Month, Avg_Sale
FROM Monthly_Avg_Sales 
WHERE Rnk = 1;



-- Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 

SELECT TOP 5(customer_id), SUM(total_sale) Total_Sales FROM retail_sales
GROUP BY customer_id
ORDER BY SUM(total_sale) DESC



-- Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.

SELECT category, COUNT(DISTINCT customer_id) Customers FROM retail_sales
GROUP BY category


-- Q.10 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)

SELECT Shifts, COUNT(*) Total_Orders FROM
(SELECT *, CASE
WHEN FORMAT(sale_time, 'hh') <=12 THEN 'Morning'
WHEN FORMAT(sale_time, 'hh') BETWEEN 12 AND 17 THEN 'Afternoon'
WHEN FORMAT(sale_time, 'hh') >= 17 THEN 'Evening'
END AS Shifts
FROM retail_sales) a
GROUP BY Shifts
```


***Findings & Insights***
- Beauty category has relatively younger customers on average.
- November is the peak month in terms of average sales, especially for Clothing.
- Top 5 customers contributed significantly to revenue.
- Evening shifts showed the highest number of transactions.
- High-value purchases (> ₹1000) are mostly in Electronics.


***Reports***
- **Sales Summary:** A detailed report summarizing total sales, customer demographics, and category performance.
- **Trend Analysis:** Insights into sales trends across different months and shifts.
- **Customer Insights:** Reports on top customers and unique customer counts per category.

***Conclusion***

This retail sales SQL analysis provides actionable insights into customer behavior, sales trends, and category performance. Through well-structured queries, businesses can identify peak periods, loyal customers, and opportunities for improvement in underperforming segments.


