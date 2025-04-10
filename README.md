#Day 3 — SQL for Data Analysis using PostgreSQL

This project contains SQL queries and outputs for analyzing a sample E-commerce dataset using PostgreSQL. It covers table creation, data insertion, and SQL-based data analysis using joins, aggregates, subqueries, views, and indexing.

## 📁 Files Included
- `ecommerce_analysis.sql` – Contains SQL code for table creation, inserts, and analysis queries.
- Output screenshots – Provided below for each step.

---

### 📦 1. Create Tables

```sql
-- Drop tables if they exist (for re-runs)
DROP TABLE IF EXISTS order_items, orders, products, customers;

-- Customers table
CREATE TABLE customers (
    cust_id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    created_at DATE
);

-- Products table
CREATE TABLE products (
    prdt_id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    category VARCHAR(50),
    price DECIMAL(10, 2)
);

-- Orders table
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    cust_id INT REFERENCES customers(cust_id),
    order_date DATE
);

-- Order Items table
CREATE TABLE order_items (
    order_item_id SERIAL PRIMARY KEY,
    order_id INT REFERENCES orders(order_id),
    prdt_id INT REFERENCES products(prdt_id),
    quantity INT,
    unit_price DECIMAL(10, 2)
);

```
![Alt text]([path_to_image](https://github.com/vickk-i/day-3/blob/main/Screenshot%202025-04-10%20121606.png?raw=true))

### 📦 2. Insert Values

```sql
-- Insert customers
INSERT INTO customers (name, email, created_at) VALUES
('Alice Smith', 'alice@example.com', '2023-01-10'),
('Bob Johnson', 'bob@example.com', '2023-02-15'),
('Carol White', 'carol@example.com', '2023-03-05');

-- Insert products
INSERT INTO products (name, category, price) VALUES
('Laptop', 'Electronics', 1200.00),
('Headphones', 'Electronics', 150.00),
('Coffee Mug', 'Home', 12.50),
('Notebook', 'Office Supplies', 5.75),
('Backpack', 'Accessories', 55.00);

-- Insert orders
INSERT INTO orders (cust_id, order_date) VALUES
(1, '2023-03-01'),
(1, '2023-04-01'),
(2, '2023-03-10'),
(3, '2023-04-05');

-- Insert order items
INSERT INTO order_items (order_id, prdt_id, quantity, unit_price) VALUES
(1, 1, 1, 1200.00),  -- Alice buys Laptop
(1, 2, 2, 150.00),   -- Alice buys 2 Headphones
(2, 3, 3, 12.50),    -- Alice buys 3 Coffee Mugs
(3, 4, 5, 5.75),     -- Bob buys 5 Notebooks
(4, 5, 1, 55.00);    -- Carol buys Backpack
```

---

### ✅ a. **SELECT, WHERE, ORDER BY, GROUP BY**

```sql
-- 1. List all products in Electronics category
SELECT * FROM products
WHERE category = 'Electronics'
ORDER BY price DESC;

-- 2. Number of orders per customer
SELECT cust_id, COUNT(*) AS total_orders
FROM orders
GROUP BY cust_id
ORDER BY total_orders DESC;
```

---

### ✅ b. **JOINS (RIGHT JOIN)**

```sql
-- 1. RIGHT JOIN: Show all orders and customer names
SELECT o.order_id, o.order_date, c.name
FROM orders o
RIGHT JOIN customers c ON o.cust_id = c.cust_id;
```

---

### ✅ c. **Subqueries**

```sql
-- 1. Customers who placed more than 1 order
SELECT name FROM customers
WHERE cust_id IN (
    SELECT cust_id
    FROM orders
    GROUP BY cust_id
    HAVING COUNT(*) > 1
);

-- 2. Products that cost more than the average price
SELECT name, price FROM products
WHERE price > (
    SELECT AVG(price) FROM products
);
```

---

### ✅ d. **Aggregate Functions (SUM, AVG)**

```sql
-- 1. Total revenue by product
SELECT p.name, SUM(oi.quantity * oi.unit_price) AS total_revenue
FROM order_items oi
JOIN products p ON oi.prdt_id = p.prdt_id
GROUP BY p.name
ORDER BY total_revenue DESC;

-- 2. Average order value per customer
SELECT c.name, AVG(oi.quantity * oi.unit_price) AS avg_order_value
FROM customers c
JOIN orders o ON c.cust_id = o.cust_id
JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY c.name;
```

---

### ✅ e. **Create Views for Analysis**

```sql
-- 1. View: customer order summary
CREATE VIEW customer_summary AS
SELECT c.cust_id, c.name, COUNT(o.order_id) AS total_orders
FROM customers c
LEFT JOIN orders o ON c.cust_id = o.cust_id
GROUP BY c.cust_id;

-- 2. View: product revenue summary
CREATE VIEW product_revenue AS
SELECT p.prdt_id, p.name, SUM(oi.quantity * oi.unit_price) AS revenue
FROM products p
JOIN order_items oi ON p.prdt_id = oi.prdt_id
GROUP BY p.prdt_id;
```

---

### ✅ f. **Optimize Queries with Indexes**

```sql
-- Index on orders.cust_id to speed up joins
CREATE INDEX idx_orders_cust_id ON orders(cust_id);

-- Index on order_items.prdt_id for faster revenue queries
CREATE INDEX idx_order_items_prdt_id ON order_items(prdt_id);
```

---

## 📸 Screenshots
- Screenshots of each command's output are included below for reference.

---

## 🧠 Summary
In this project, we:
- Designed and populated a PostgreSQL e-commerce database with four tables: `customers`, `products`, `orders`, and `order_items`.
- Analyzed data using SQL queries, including joins (RIGHT JOIN), aggregate functions, and subqueries.
- Created views to simplify reporting and optimized queries using indexes.
- Practiced real-world data extraction and reporting.

---

Let me know if you need any further changes!
