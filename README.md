# 📚Lola-s-Book-store-Analysis

## 📌 Project Overview

This project is a comprehensive SQL-based analysis of **Lola’s Book Store**, designed to demonstrate practical MySQL skills using a realistic transactional dataset.  
The analysis covers **database design, data cleaning, validation, and business-focused insights** using beginner to advanced SQL techniques.


---

## Database & Tools
- **Database:** MySQL
- **Interface:** MySQL Workbench
- **Language:** SQL

---

## Database Structure
-File: ![Dataset]()
### 1. Customers Table
Stores customer demographic and signup information.

**Columns:**
- `customer_id` (Primary Key)
- `customer_name`
- `email`
- `city`
- `signup_date`

---

### 2. Books Table
Contains inventory and pricing information for books sold.

**Columns:**
- `book_id` (Primary Key)
- `title`
- `author`
- `genre`
- `price`
- `stock_quantity`

---

### 3. Orders Table
Stores transactional order data.

**Columns:**
- `order_id` (Primary Key)
- `customer_id` (Foreign Key)
- `book_id` (Foreign Key)
- `order_date`
- `quantity`
- `payment_method`
- `order_status`

---

## Data Exploration & Cleaning

The following checks were performed before analysis:

### ✔ Data Inspection
- Previewed all tables to understand structure and contents.
- Verified successful data import.

### ✔ NULL Value Checks
- Checked for NULL values across all critical columns in `customers` and `orders`.
- Observed that NULLs represent missing or unreported data and were **intentionally retained** to preserve dataset realism.

### ✔ Duplicate Checks
- Used window functions (`ROW_NUMBER`) to identify potential duplicates.
- No duplicate records were found.

### ✔ Case Sensitivity Checks
- Reviewed distinct values for:
  - `city`
  - `payment_method`
  - `order_status`
- Standardized text fields by converting `payment_method` and `order_status` to lowercase to ensure consistency.

### ✔ Business Rule Validation
- Checked for orders placed **before customer signup dates**.
- Flagged such cases for awareness rather than deletion.

---

## Analysis Objectives

The analysis aimed to:
- Understand customer purchasing behavior
- Evaluate revenue performance
- Analyze order trends across time
- Segment customers by value
- Assess inventory and genre performance

---

## Key Analysis Questions Answered

The project answers **27 structured questions**, ranging from beginner to advanced SQL concepts, including:

- Customer and order counts
- Revenue calculations
- Genre and book performance
- Customer segmentation
- Time-based trends (monthly and yearly)
- Order status impact on revenue
- Running total revenue analysis
- Customer behavior across genres
- Identification of inactive customers

---

## Key Findings

- Revenue is concentrated among a small group of high-value customers.
- Certain genres consistently outperform others in revenue contribution.
- Order status significantly impacts realized revenue.
- Monthly revenue trends show variation across the 2024–2025 period.
- A subset of customers signed up but never placed any orders.
- Some books exhibit strong sales relative to overall averages.
- A small percentage of customers account for a large share of revenue
- Completed orders contribute the vast majority of revenue
- A noticeable number of users sign up but never convert to buyers
---

## Assumptions & Limitations

- NULL values were treated as missing data and not imputed.
- The dataset is a simulated retail environment and does not represent real business operations.
- Inventory restocking and refunds beyond order status were not modeled.

---

## Skills Demonstrated

- Relational database design
- Data cleaning and validation
- Business-oriented SQL analysis
- Use of window functions
- Professional SQL documentation
- MySQL best practices

---

## Conclusion

This project demonstrates the ability to handle a complete SQL workflow — from raw data ingestion to actionable insights — using realistic datasets and business logic.  
It is designed as a **portfolio-ready project** showcasing analytical thinking, SQL proficiency, and clean documentation.

---
## Data setup& SQL queries
### create database
```sql
       create database Lola_Bookstore;
        use Lola_bookstore;
```
### Table Creation
   #### Customers Table
   ```sql
         CREATE TABLE customers (
    customer_id INT PRIMARY KEY,
    customer_name VARCHAR(20),
    email VARCHAR(30),
    city VARCHAR(20),
    signup_date DATE
);

-- Books Table
CREATE TABLE books (
    book_id INT PRIMARY KEY,
    title VARCHAR(20),
    author VARCHAR(20),
    genre VARCHAR(20),
    price NUMERIC,
    stock_quantity NUMERIC
);

Orders Table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT REFERENCES customers(customer_id),
    book_id INT REFERENCES books(book_id),
    order_date DATE,
    quantity NUMERIC,
    payment_method VARCHAR(20),
    order_status VARCHAR(20)
);
```
  ### Data Exploration
```sql
 SELECT * FROM books;
SELECT * FROM orders;
SELECT * FROM customers;
``` 
### Data Quality Checks
#### NULL Checks
``` sql
 SELECT * FROM orders
WHERE order_id IS NULL
   OR customer_id IS NULL
   OR book_id IS NULL
   OR order_date IS NULL
   OR quantity IS NULL
   OR payment_method IS NULL
   OR order_status IS NULL;

SELECT * FROM customers
WHERE customer_id IS NULL
   OR customer_name IS NULL
   OR email IS NULL
   OR city IS NULL
   OR signup_date IS NULL;
```

### Duplicate Checks
```sql
WITH dupl AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY customer_id, customer_name) AS rnk
    FROM customers
)
SELECT * FROM dupl WHERE rnk = 2;

WITH dupli AS (
    SELECT *, ROW_NUMBER() OVER (PARTITION BY order_id) AS rnk
    FROM orders
)
SELECT * FROM dupli WHERE rnk = 2;
```
### Case Sensitivity Checks & Cleaning
```sql
SELECT DISTINCT city FROM customers;
SELECT DISTINCT payment_method FROM orders;
SELECT DISTINCT order_status FROM orders;
 UPDATE orders
SET payment_method = LOWER(payment_method),
    order_status = LOWER(order_status);
```
### Business Rule Validation
```sql
SELECT COUNT(*) AS total
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE o.order_date < c.signup_date;
```
### Analysis Queries
- Q1. Total Customers
``` sql
SELECT COUNT(DISTINCT customer_name) FROM customers;
```
- Q2. Total Books
```sql
SELECT COUNT(title) AS num_of_books FROM books;
```
- Q3. Total Orders
```sql
SELECT COUNT(order_id) AS total_orders FROM orders;
```
- Q4. Order Date Range
```sql
SELECT MIN(order_date) AS first_orderdate,
       MAX(order_date) AS last_orderdate
FROM orders;
```
- Q5. Distinct Genres
```sql
SELECT COUNT(DISTINCT genre) FROM books;
```
- Q6. Order Statuses
```sql
SELECT DISTINCT order_status FROM orders;
```
- Q7. Orders per Status
```sql
SELECT order_status, COUNT(*) AS total
FROM orders
GROUP BY order_status;
```
- Q8. Payment Methods Usage
```sql
SELECT payment_method, COUNT(*) AS total
FROM orders
GROUP BY payment_method;
```
- Q9. Total Revenue
```sql
SELECT SUM(price * o.quantity) AS total_revenue
FROM books
JOIN orders o USING(book_id);
```
- Q10. Revenue per Book
```sql
SELECT title, SUM(price * o.quantity) AS revenue
FROM books
JOIN orders o USING(book_id)
GROUP BY title;
```
- Q11. Highest Quantity Sold
```sql
SELECT title, SUM(o.quantity) AS quantity_sold
FROM books
JOIN orders o USING(book_id)
GROUP BY title
ORDER BY quantity_sold DESC
LIMIT 1;
```
- Q12. Top Revenue Genre
```sql
SELECT genre, SUM(price * o.quantity) AS revenue
FROM books
JOIN orders o USING(book_id)
GROUP BY genre
ORDER BY revenue DESC
LIMIT 1;
```
- Q13. Average Order Quantity
```sql
SELECT AVG(o.quantity) AS avg_order_quantity
FROM orders o;
```
- Q14. Repeat Customers
```sql
SELECT customer_name, COUNT(order_id) AS total_orders
FROM customers
JOIN orders USING(customer_id)
GROUP BY customer_name
HAVING total_orders > 1;
```
- Q15. Monthly Order Trend
```sql
SELECT DATE_FORMAT(order_date, '%Y-%m') AS year_month,
       COUNT(order_id) AS orders
FROM orders
GROUP BY year_month
ORDER BY year_month;
```
- Q16. Revenue by Order Status
```sql
SELECT order_status, SUM(price * o.quantity) AS revenue
FROM orders o
JOIN books USING(book_id)
GROUP BY order_status;
```
- Q17. Top Revenue City
```sql
SELECT city, SUM(price * o.quantity) AS revenue
FROM customers
JOIN orders o USING(customer_id)
JOIN books USING(book_id)
GROUP BY city
ORDER BY revenue DESC
LIMIT 1;
```
- Q18. Top 5 Customers by Spend
```sql
SELECT customer_name, SUM(price * o.quantity) AS total_spent
FROM customers
JOIN orders o USING(customer_id)
JOIN books USING(book_id)
GROUP BY customer_name
ORDER BY total_spent DESC
LIMIT 5;
```
- Q19. Completed Orders Revenue %
```sql
SELECT ROUND(
    (SUM(CASE WHEN order_status = 'completed'
        THEN quantity * price ELSE 0 END) /
     SUM(quantity * price)) * 100, 2
) AS completed_order_revenue_pct
FROM orders o
JOIN books b ON o.book_id = b.book_id;
```
- Q20. Rank Books by Revenue
```sqql
WITH bookgen AS (
    SELECT genre, SUM(price * o.quantity) AS revenue
    FROM books
    JOIN orders o USING(book_id)
    GROUP BY genre
)
SELECT *, RANK() OVER (ORDER BY revenue DESC) AS rev_rnk
FROM bookgen;
```
- Q21. Revenue by Year
```sql
SELECT YEAR(order_date) AS yr,
       SUM(price * o.quantity) AS revenue
FROM books
JOIN orders o USING(book_id)
GROUP BY yr;
```
- Q22. Cancelled/Returned Percentage
```sql
SELECT ROUND(
    (SUM(CASE WHEN order_status IN ('cancelled','returned') THEN 1 ELSE 0 END)
    / COUNT(*)) * 100, 2
) AS cancelled_or_returned_percentage
FROM orders;
```
- Q23. Customers with No Orders
```sql
SELECT customer_name
FROM customers c
LEFT JOIN orders o USING(customer_id)
WHERE o.order_id IS NULL;
```
- Q24. Running Total Revenue by Month
```sql
SELECT yr_mnth,
       revenue,
       SUM(revenue) OVER (ORDER BY yr_mnth) AS running_total
FROM (
    SELECT DATE_FORMAT(order_date, '%Y-%m') AS yr_mnth,
           SUM(price * o.quantity) AS revenue
    FROM books
    JOIN orders o USING(book_id)
    GROUP BY yr_mnth
) t
ORDER BY yr_mnth;
```
- Q25. Customer Segmentation
```sql
SELECT customer_name,
       SUM(price * o.quantity) AS total_spent,
       CASE
           WHEN SUM(price * o.quantity) > 750 THEN 'high value'
           WHEN SUM(price * o.quantity) > 500 THEN 'medium value'
           ELSE 'low value'
       END AS spend_category
FROM customers
JOIN orders o USING(customer_id)
JOIN books USING(book_id)
GROUP BY customer_name
ORDER BY total_spent DESC;
```
- Q26.Find customers who have ordered books from more than one genre.
```sql
		select customer_name,count(distinct genre) as orders from customers 
        join orders using(customer_id)
        join books using(book_id)
        group by customer_name
        having orders>1;
```
- Q27. Identify books whose total sold quantity is above the average quantity sold per book.
 ```sql
            select title,sum(o.quantity) as total quantity from books 
             join orders o using (book_id)
             group by title
             having sum(o.quantity)>(select avg(quantity) from orders);
```
