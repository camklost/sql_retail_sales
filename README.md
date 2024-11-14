# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis  
**Level**: Beginner  
**Database**: `sql_project`

This project showcases foundational SQL techniques for data analysis, focusing on cleaning, exploring, and analyzing retail sales data. The objective is to build a structured retail sales database, conduct exploratory data analysis (EDA), and address key business questions through SQL queries.

## Project Objectives

1. **Database Setup:** Initialize a `sql_project` database and create a structured table for retail sales data.
2. **Data Cleaning**: Identify and remove any records with missing values to ensure data quality.
3. **Exploratory Data Analysis (EDA)**: Perform essential analyses to understand data patterns and insights.
4. **Business Analysis**: Execute SQL queries to answer specific business questions and derive actionable insights.

## Project Structure

### 1. Database Setup

- **Database Creation**: Initialize the `sql_project` database.
- **Table Creation**: Build a `retail_sales` table with columns for transaction details, including transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity, price per unit, cost of goods sold (COGS), and total sale amount.

```sql
CREATE DATABASE sql_project;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(15),
    age INT,
    category VARCHAR(15),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Cleaning and Exploration

- **Record Count**: Determine the total number of records in the dataset.
- **Customer Count**: I dentify the number of unique customers.
- **Category Count**: List all product categories in the dataset.
- **Null Check and Removal**: Identify and delete records with missing values to maintain data integrity.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL
    OR sale_time IS NULL
    OR customer_id IS NULL
    OR gender IS NULL
    OR age IS NULL
    OR category IS NULL
    OR quantity IS NULL
    OR price_per_unit IS NULL
    OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
   sale_date IS NULL
    OR sale_time IS NULL
    OR customer_id IS NULL
    OR gender IS NULL
    OR age IS NULL
    OR category IS NULL
    OR quantity IS NULL
    OR price_per_unit IS NULL
    OR cogs IS NULL;
```

### 3. Data Analysis & Findings

The following SQL queries address specific business questions:

1. **Sales on a Specific Date**
   
    Retrieve all columns for sales made on '2022-11-05'.
```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **High-Quantity Clothing Sales in November 2022**
   
    Retrieve transactions in the 'Clothing' category with quantity ≥ 4 in November 2022.
```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
      AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11'
      AND quantity >= 4;
```

3. **Total Sales per Category**
   
    Calculate total sales and order count for each category 
```sql
SELECT 
    category,
    SUM(total_sale) as total_sales,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY category
```

4. **Average Age of 'Beauty' Category Buyers**

    Calculate the average age of customers purchasing 'Beauty' items
```sql
SELECT ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

5. **High-Value Transactions**

    List transactions where total sale > 1000
```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000
```

6. **Transactions by Gender and Category**

    Count transactions per gender in each category. 
```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_transactions
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

7. **Best-Selling Month by Year**

    Identify the month with the highest average sale each year.
       
```sql
SELECT year, month, avg_sales
FROM (
    SELECT EXTRACT(YEAR FROM sale_date) AS year,
           EXTRACT(MONTH FROM sale_date) AS month,
           AVG(total_sale) AS avg_sales,
           RANK() OVER (PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) AS rank
    FROM retail_sales
    GROUP BY year, month
) AS t1
WHERE rank = 1;

```

8. **Top 5 Customers by Sales **

    List the top 5 customers based on total sales
       
```sql
SELECT customer_id, SUM(total_sale) AS total_sales
FROM retail_sales
GROUP BY customer_id
ORDER BY total_sales DESC
LIMIT 5;
```

9. **Unique Customers by Category**

    Count unique customers per product category.
   
```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) AS no_customers
FROM retail_sales
GROUP BY category
```

10. **Orders by Shift**

    Create shifts (morning, afternoon, evening) and count orders in each shift.
  
```sql
WITH hourly_sales AS(
		SELECT *,
		CASE
			WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'morning'
			WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'afternoon'
			ELSE 'evening'
		END AS shift
	FROM retail_sales
)
SELECT 
	shift,
	COUNT(transactions_id) AS no_orders
FROM hourly_sales
GROUP BY shift
```

## Findings

- **Customer Demographics**: Customers span various age groups, with notable sales across categories like Clothing and Beauty.
- **High-Value Transactions**: High sales (>1000) indicate premium transactions.
- **Sales Trends**: Monthly sales trends reveal peak seasons, guiding inventory and marketing.
- **Top Customers**: Identifying top customers and unique customer counts provides valuable insights for customer relationship strategies.


## Conclusion

This project provides a thorough introduction to SQL for data analysis. It covers database setup, data cleaning, exploratory analysis, and business-focused queries. Insights from this project can support retail businesses in optimizing strategies around customer demographics, product performance, and sales trends.


