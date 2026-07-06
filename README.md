# Retail Sales SQL Project

## Project Overview
This project analyzes retail sales data using SQL to answer business questions.

## Dataset
- 2,000+ retail transactions
- Customer demographics
- Product categories
- Sales and profit

## SQL Concepts Used
- SELECT
- WHERE
- GROUP BY
- HAVING
- ORDER BY
- CASE
- Aggregate Functions
- Window Functions
- Common Table Expressions (CTEs)

## Business Questions Solved
- Highest revenue category
- Top 10 customers
- Repeat customers
- Revenue by gender
- Monthly sales analysis
- Best-selling month
- Profit by category
- Average order value

## Tools Used
- MySQL
- GitHub

## Project Structure
```
dataset/
sql_queries/
screenshots/
README.md
```

**--- SQL Retail sales Analysis P2**

CREATE DATABASE SQL_PROJECT_P2;

USE SQL_PROJECT_P2;

**--- Create Table**

CREATE TABLE retail_sales_tb
             (
               transactions_id INT PRIMARY KEY,
			   sale_date DATE,
               sale_time TIME,
               customer_id	INT,
			   gender	VARCHAR (15),
               age	INT,
               category	VARCHAR (15),
               quantiy	INT, 
               price_per_unit	FLOAT,
               cogs	FLOAT,
               total_sale FLOAT
               );
               
SELECT * FROM retail_sales_tb
;

SELECT * FROM retail_sales_tb
WHERE transactions_id is null;

SELECT * FROM retail_sales_tb
WHERE sale_date is null;

SELECT * FROM retail_sales_tb
WHERE sale_time is null;

SELECT * FROM retail_sales_tb
WHERE price_per_unit is null;

SELECT * FROM retail_sales_tb
WHERE category is null
	OR sale_date is null 
    OR sale_time is null
    OR price_per_unit is null
    OR customer_id is null
    OR gender is null
    OR age is null
    OR quantiy is null
    OR price_per_unit is null
    OR cogs is null
    OR total_sale is null;
    
** --- Data Exploaration **  
 
 --- How many Total sales we have?
 
SELECT COUNT(*) AS Total_sales FROM retail_sales_tb;
    
 ****--- How many customers we have?
 
SELECT COUNT(distinct customer_id) AS Unique_customer_count FROM retail_sales_tb;

SELECT COUNT(distinct category) AS Unique_category FROM retail_sales_tb;
  

---** Data Analysis & Business Key Problems & Answers**

--- Sales Performance Analysis

-- Q.1 Write a SQL query to retrieve all columns for sales made on '2022-11-05

SELECT * 
FROM retail_sales_tb
WHERE sale_date = '2022-11-05';

-- Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 10 in the month of Nov-2022

SELECT *
FROM retail_sales_tb
WHERE Category = 'Clothing'
  AND Quantiy > 10
  AND sale_date >= '2022-11-01'
  AND sale_date < '2022-12-01';

-- Q3- Which category generated the highest revenue?

SELECT category , 
	   SUM(total_sale) AS Total_sales
FROM retail_sales_tb
GROUP BY Category
ORDER BY Total_sales DESC
LIMIT 1 ;

-- Q.4 Write a SQL query to calculate the total sales (total_sale) for each category.

SELECT Category, 
      SUM(total_sale) AS Category_wise_sale
FROM  retail_sales_tb    
GROUP BY Category
ORDER BY Category_wise_sale DESC;

-- Q.5 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.

SELECT ROUND(AVG(age) , 2) AS Average_Age
FROM retail_sales_tb  
WHERE Category = 'Beauty';

-- Q.6 Write a SQL query to find all transactions where the total_sale is greater than 1000.

SELECT *    
FROM retail_sales_tb 
WHERE total_sale > 1000;     
    
-- Q.7 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.

SELECT Category,
       gender, 
       COUNT(transactions_id) AS total_order
FROM  retail_sales_tb 
GROUP BY Category , gender
ORDER BY Category, gender;

-- Q.8- Which category sold the highest quantity?

SELECT category , 
	   COUNT(quantiy) AS Total_qty
FROM retail_sales_tb
GROUP BY Category
ORDER BY Total_qty DESC
LIMIT 1 ;

-- Q.9- What is the average order value for each category?

SELECT category,
       ROUND(AVG(total_sale)) AS Avg_order_Value
FROM retail_sales_tb
GROUP BY category
ORDER BY Avg_order_Value DESC;      

-- Q.10- Which category has the highest profit (using total_sale - cogs)?

SELECT category,
      ROUND(SUM(total_sale - cogs)) AS Total_Profit
FROM retail_sales_tb
GROUP BY category
ORDER BY Total_Profit DESC
LIMIT 1;       

-- Q.11- Which category has the highest average selling price?

SELECT category,
      ROUND(AVG(price_per_unit)) AS Average_Sale_Price
FROM retail_sales_tb
GROUP BY category
ORDER BY Average_Sale_Price DESC
LIMIT 1;  


-- Q.12- How many unique customers made purchases?

SELECT COUNT(DISTINCT(customer_id)) AS Unique_customer
FROM retail_sales_tb;

-- Q.13- Which customer spent the most?

SELECT customer_id,
       SUM(total_sale) AS Total_spent
FROM retail_sales_tb
GROUP BY customer_id 
ORDER BY Total_spent DESC
LIMIT 1;

-- Q.14- Top 10 customers by revenue.

SELECT customer_id,
       SUM(total_sale) AS Total_Revenue
FROM retail_sales_tb
GROUP BY customer_id 
ORDER BY Total_Revenue DESC
LIMIT 10;

-- Q.15- Which customer purchased the most items?

SELECT customer_id, 
	   SUM(quantiy) AS Most_purchase
FROM retail_sales_tb    
GROUP BY customer_id 
ORDER BY Most_purchase
LIMIT 1;

-- Q 16- Repeat customers (customers with more than one purchase).

SELECT customer_id,
       COUNT(*) AS Total_Purchase
FROM retail_sales_tb
GROUP BY  customer_id
HAVING COUNT(*) > 1
ORDER BY Total_Purchase DESC;

-- Q.17- Customers who purchased from multiple categories.

SELECT customer_id,
       COUNT(DISTINCT Category) AS Category_wise_Purchase
FROM retail_sales_tb
GROUP BY  customer_id
HAVING COUNT(DISTINCT Category) > 1
ORDER BY Category_wise_Purchase DESC , customer_id;


-- Q.18- Revenue by gender?

SELECT gender, 
       SUM(total_sale) AS Total_Revenue
FROM retail_sales_tb    
GROUP BY gender
ORDER BY Total_Revenue DESC;

-- Q.19- Average purchase amount by gender?

SELECT gender, 
       ROUND (AVG(total_sale)) AS Avg_Purchase_genderwise
FROM retail_sales_tb    
GROUP BY gender
ORDER BY  Avg_Purchase_genderwise DESC;

-- Q.20 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year

SELECT Year(sale_date) AS Year,
       MONTH(sale_date) AS Month,
       ROUND(AVG(total_sale) , 2) AS Total_sales
FROM retail_sales_tb
GROUP BY  Year(sale_date) , MONTH(sale_date)
ORDER BY Year , month;      

-- Q.21 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)

WITH Hourly_Sale  AS
     ( SELECT * , 
          CASE 
              WHEN EXTRACT(Hour from sale_time) < 12 THEN 'Morning'
              WHEN EXTRACT(Hour from sale_time) BETWEEN 12 AND 17 THEN 'AfternoOn'
		ELSE 'Evening'
    END AS Shift
 FROM retail_sales_tb
 )
 SELECT Shift,
      COUNT(*) AS Total_Orders
FROM Hourly_Sale 
GROUP BY Shift;   
      
     
-- END OF PROJECT

