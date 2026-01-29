# SQL Retail Sales Analysis Project

## Overview

**Project Name**: SQL Retail Sales Analysis  
**Difficulty Level**: Beginner  
**Database Name**: `p1_retail_db`

This project focuses on applying core SQL concepts to analyze retail sales data. It simulates real-world tasks performed by data analysts, such as preparing data, exploring trends, and answering business-related questions using structured queries. The project is well-suited for beginners who want hands-on SQL practice through a complete mini case study.

---

## Project Goals

- Create and manage a retail sales database
- Clean and validate raw sales data
- Perform exploratory analysis to understand patterns
- Solve practical business problems using SQL

---

## Database Design

### Database and Table Creation

A database named `p1_retail_db` is created, containing a single table `retail_sales` that stores transaction-level information.

```sql
CREATE DATABASE p1_retail_db;

CREATE TABLE retail_sales (
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

---

## Data Validation & Preparation

Before analysis, the dataset is reviewed to ensure data quality.

Key checks performed:
- Total number of transactions
- Count of distinct customers
- Identification of product categories
- Detection and removal of records with missing values

```sql
SELECT COUNT(*) FROM retail_sales;
SELECT COUNT(DISTINCT customer_id) FROM retail_sales;
SELECT DISTINCT category FROM retail_sales;

DELETE FROM retail_sales
WHERE sale_date IS NULL OR sale_time IS NULL OR customer_id IS NULL
   OR gender IS NULL OR age IS NULL OR category IS NULL
   OR quantity IS NULL OR price_per_unit IS NULL OR cogs IS NULL;
```

---

## Business Questions & SQL Analysis

### 1. Daily Sales Lookup
Retrieve all transactions that occurred on **5 November 2022**.

```sql
SELECT *
FROM retail_sales
WHERE sale_date = '2022-11-05';
```

### 2. High-Volume Clothing Sales (Nov 2022)
Find clothing purchases where more than four items were sold during November 2022.

```sql
SELECT *
FROM retail_sales
WHERE category = 'Clothing'
  AND quantity > 4
  AND TO_CHAR(sale_date, 'YYYY-MM') = '2022-11';
```

### 3. Revenue by Product Category
Calculate total revenue and order count for each category.

```sql
SELECT category,
       SUM(total_sale) AS total_revenue,
       COUNT(*) AS order_count
FROM retail_sales
GROUP BY category;
```

### 4. Average Customer Age (Beauty Products)
Determine the average age of customers purchasing beauty-related products.

```sql
SELECT ROUND(AVG(age), 2) AS average_age
FROM retail_sales
WHERE category = 'Beauty';
```

### 5. Large-Value Transactions
List transactions with a total sale amount exceeding 1000.

```sql
SELECT *
FROM retail_sales
WHERE total_sale > 1000;
```

### 6. Gender-wise Transaction Distribution
Analyze how transactions are distributed across gender and category.

```sql
SELECT category, gender, COUNT(*) AS transaction_count
FROM retail_sales
GROUP BY category, gender
ORDER BY category;
```

### 7. Best Performing Month per Year
Identify the highest average sales month for each year.

```sql
SELECT year, month, avg_sale
FROM (
    SELECT EXTRACT(YEAR FROM sale_date) AS year,
           EXTRACT(MONTH FROM sale_date) AS month,
           AVG(total_sale) AS avg_sale,
           RANK() OVER (
               PARTITION BY EXTRACT(YEAR FROM sale_date)
               ORDER BY AVG(total_sale) DESC
           ) AS rnk
    FROM retail_sales
    GROUP BY 1, 2
) ranked
WHERE rnk = 1;
```

### 8. Top Customers by Revenue
Identify the five customers with the highest total spending.

```sql
SELECT customer_id, SUM(total_sale) AS total_spent
FROM retail_sales
GROUP BY customer_id
ORDER BY total_spent DESC
LIMIT 5;
```

### 9. Customer Reach per Category
Count unique customers in each product category.

```sql
SELECT category, COUNT(DISTINCT customer_id) AS unique_customers
FROM retail_sales
GROUP BY category;
```

### 10. Order Volume by Time of Day
Classify orders into shifts based on transaction time.

```sql
WITH time_segments AS (
    SELECT *,
           CASE
               WHEN EXTRACT(HOUR FROM sale_time) < 12 THEN 'Morning'
               WHEN EXTRACT(HOUR FROM sale_time) BETWEEN 12 AND 17 THEN 'Afternoon'
               ELSE 'Evening'
           END AS time_shift
    FROM retail_sales
)
SELECT time_shift, COUNT(*) AS order_volume
FROM time_segments
GROUP BY time_shift;
```

---

## Key Insights

- Sales activity spans multiple age groups and product categories
- A subset of transactions contributes significantly to revenue
- Clear seasonal patterns emerge at the monthly level
- Certain customers consistently generate higher sales

---

## Summary

This project demonstrates how SQL can be used end-to-end for retail data analysis — from database creation to generating actionable insights. It reflects common analytical workflows and strengthens essential SQL skills for entry-level data analyst roles.

---

## Usage Instructions

1. Create the database using the provided SQL script
2. Load sales data into the `retail_sales` table
3. Execute analysis queries to explore insights
4. Extend the analysis with additional business questions

---

## Author

**Zero Analyst**  
A SQL-focused data analytics project created for learning, practice, and portfolio demonstration.

---

⭐ If you find this project useful, feel free to explore, modify, and build upon it.

