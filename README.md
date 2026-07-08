# Retail Sales Analysis SQL Project

## Project Overview

**Project Title**: Retail Sales Analysis
**Database**: `SQL - Retail Sales Analysis_utf`

This project demonstrates the fundamental SQL techniques used by data analysts to manage, explore, clean, and analyze retail sales data. It covers the complete workflow, from creating and populating a retail sales database to performing exploratory data analysis (EDA) and solving real-world business problems using SQL queries. It is well-suited for beginners looking to strengthen their SQL skills through practical, hands-on experience.

## Objectives

1. **Database Setup**: Create and populate a retail sales database using the provided dataset.
2. **Data Cleaning**: Detect and remove records containing missing or null values to ensure data quality.
3. **Exploratory Data Analysis (EDA)**: Examine the dataset to gain a better understanding of its structure and key characteristics.
4. **Business Analysis**: Analyze the data by writing SQL queries to answer business-related questions and uncover meaningful insights.

## Project Structure

### 1. Database Setup

* **Database Creation**: Begin by creating a database named `p_sales_retails`.
* **Table Creation**: Create a table called `retail_sales` to store the retail sales records. The table contains fields for transaction ID, sale date, sale time, customer ID, gender, age, product category, quantity sold, price per unit, cost of goods sold (COGS), and total sales amount.

```sql
CREATE DATABASE p_sales_retails;

CREATE TABLE retail_sales
(
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
```

### 2. Data Exploration & Cleaning

The following steps are performed to explore and prepare the dataset for analysis:

* **Record Count**: Calculate the total number of records available in the dataset.
* **Customer Count**: Determine the number of unique customers.
* **Category Count**: List all distinct product categories.
* **Null Value Check**: Identify and remove records containing null or missing values.

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

SELECT * FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;

DELETE FROM retail_sales
WHERE 
    sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL OR 
    gender IS NULL OR age IS NULL OR category IS NULL OR 
    quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

### 3. Data Analysis & Findings

The following SQL queries address a variety of business questions and provide valuable insights from the retail sales dataset.

1. **Retrieve all sales transactions that occurred on '2022-11-05'.**

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

2. **Retrieve all Clothing category transactions where more than 4 units were sold during November 2022.**

```sql
SELECT 
  *
FROM retail_sales
WHERE 
    category = 'Clothing'
    AND 
    quantiy >= 4
    AND
	DATE_FORMAT(sale_date, '%b-%Y') = 'Nov-2022';
```

3. **Calculate the total sales amount for each product category.**

```sql
SELECT 
    category,
    SUM(total_sale) as net_sale,
    COUNT(*) as total_orders
FROM retail_sales
GROUP BY 1
```

4. **Find the average age of customers who purchased products from the 'Beauty' category.**

```sql
SELECT
    ROUND(AVG(age), 2) as avg_age
FROM retail_sales
WHERE category = 'Beauty'
```

5. **Retrieve all transactions where the total sale amount exceeds 1000.**

```sql
SELECT * FROM retail_sales
WHERE total_sale > 1000
```

6. **Calculate the total number of transactions made by each gender across every product category.**

```sql
SELECT 
    category,
    gender,
    COUNT(*) as total_trans
FROM retail_sales
GROUP 
    BY 
    category,
    gender
ORDER BY 1
```

7. **Determine the average monthly sales and identify the highest-performing month for each year.**

```sql
SELECT 
       year,
       month,
    avg_sale
FROM 
(    
SELECT 
    EXTRACT(YEAR FROM sale_date) as year,
    EXTRACT(MONTH FROM sale_date) as month,
    AVG(total_sale) as avg_sale,
    RANK() OVER(PARTITION BY EXTRACT(YEAR FROM sale_date) ORDER BY AVG(total_sale) DESC) as rank
FROM retail_sales
GROUP BY 1, 2
) as t1
WHERE rank = 1
```

8. **Identify the top five customers based on their total purchase value.**

```sql
SELECT 
    customer_id,
    SUM(total_sale) as total_sales
FROM retail_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```

9. **Count the number of unique customers who made purchases within each product category.**

```sql
SELECT 
    category,    
    COUNT(DISTINCT customer_id) as cnt_unique_cs
FROM retail_sales
GROUP BY category
```

10. **Categorize orders into Morning, Afternoon, and Evening shifts, then calculate the number of orders for each shift.**

```sql
WITH hourly_sale
AS
(
SELECT *,
    CASE
        WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
        WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
        ELSE 'Evening'
    END as shift
FROM retail_sales
)
SELECT 
    shift,
    COUNT(*) as total_orders    
FROM hourly_sale
GROUP BY shift
```

## Findings

* **Customer Demographics**: Customers represent a wide range of age groups, with purchases spread across multiple product categories, including Clothing and Beauty.
* **High-Value Transactions**: The dataset contains several transactions with sales exceeding 1000, highlighting premium-value purchases.
* **Sales Trends**: Monthly sales analysis reveals seasonal variations and helps identify periods of peak business performance.
* **Customer Insights**: The analysis highlights the highest-spending customers and identifies the categories with the greatest customer engagement.

## Reports

* **Sales Summary**: Provides an overview of total sales, customer demographics, and category-wise performance.
* **Trend Analysis**: Examines sales patterns across different months and time-of-day shifts.
* **Customer Insights**: Summarizes top customers and the number of unique buyers within each product category.

## Conclusion

This project offers a practical introduction to SQL for aspiring data analysts by covering database creation, data cleaning, exploratory analysis, and business-oriented querying. The insights generated through these analyses demonstrate how SQL can be used to uncover sales trends, understand customer purchasing behavior, and evaluate product performance, ultimately supporting informed business decision-making.
