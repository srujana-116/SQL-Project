Online Shopping Management System (MySQL)

## 1. Project Description

The **Online Shopping Management System** is a database-focused project
developed using MySQL. It stores and manages information related to
customers, products, orders, order items, and payments.

The database is designed to demonstrate how a typical online shopping
platform can organize related information across multiple tables. SQL
queries are used to retrieve customer details, track orders, analyze
product sales, review payment information, and generate useful business
reports.

This project focuses on **database design, data population, and SQL
querying**. It is implemented using MySQL without a separate front-end
application.

## 2. Features

-   Store customer details, including name, email, phone number, city,
    and registration date.
-   Maintain product information, categories, suppliers, prices, and
    available stock.
-   Record customer orders and their current status.
-   Store the products and quantities included in each order.
-   Track payment dates, amounts, payment methods, and payment statuses.
-   Retrieve related information using `INNER JOIN` and `LEFT JOIN`.
-   Analyze data using aggregate functions such as `COUNT()`, `SUM()`,
    `AVG()`, `MIN()`, and `MAX()`.
-   Use subqueries and correlated subqueries for more advanced
    filtering.
-   Create views for reusable reports.
-   Use stored procedures to perform reusable database operations.
-   Generate reports about customers, products, orders, sales, and
    payments.

## 3. Technologies Used

  Technology                   Purpose
  MySQL                        Relational database management system
  SQL                          Creating tables, inserting records, querying, and reporting
  MySQL Workbench (optional)   Writing and executing SQL scripts

## 4. Database Structure

The project contains five related tables.
Customers: Stores customer details such as name, email, phone number, and city.
Products: Stores product details such as name, category, price, and stock quantity.
Orders: Stores customer order details, including order date and order status.
Order_Items: Stores the products and quantities included in each order.
Payments: Stores payment details such as amount, payment method, date, and status.

### 4.6 Table Relationships

-   One customer can place multiple orders.
-   One order can contain multiple order items.
-   One product can appear in multiple order items.
-   Each payment record is associated with an order.

``` text
Customers
   |
   | 1-to-many
   v
 Orders
   |
   | 1-to-many
   v
Order_Items
   ^
   | many-to-1
Products

Orders
   |
   | 1-to-many (as defined by the foreign key)
   v
Payments

**Primary keys and foreign keys** maintain relationships and help
preserve referential integrity. The current sample data uses one payment
record per order, although the foreign-key design permits multiple
payment records for an order.

## 5. Installation and Setup

### Prerequisites

-   MySQL Server 8.0 or a compatible MySQL version.
-   MySQL Workbench or another MySQL client.
-   Basic knowledge of SQL.

### Steps

1.  Install and configure MySQL Server.
2.  Open MySQL Workbench and connect to your local MySQL server.
3.  Create a new SQL script.
4.  Create a database for the project:

``` sql
CREATE DATABASE online_shopping;
```

5.  Select the database:

``` sql
USE online_shopping;
```

6.  Execute the table creation statements in this order:
    -   `Customers`
    -   `Products`
    -   `Orders`
    -   `Order_Items`
    -   `Payments`
7.  Execute the `INSERT INTO` statements to populate the tables.
8.  Verify that the records were inserted successfully:

``` sql
SHOW TABLES;

SELECT * FROM Customers;
SELECT * FROM Products;
SELECT * FROM Orders;
SELECT * FROM Order_Items;
SELECT * FROM Payments;
```

**Important:** Create parent tables before dependent tables. For this
schema, create `Customers` and `Products` before `Orders`; create
`Orders` before `Order_Items` and `Payments`.

## 6. How to Execute SQL Files

If the project SQL scripts are saved as separate files, execute them in
the following order:

1.  `01_create_database.sql` -- creates and selects the database.
2.  `02_create_tables.sql` -- creates the five tables.
3.  `03_insert_data.sql` -- inserts sample records.
4.  `04_queries.sql` -- contains practice and reporting queries.
5.  `05_views.sql` -- creates database views.
6.  `06_stored_procedures.sql` -- creates stored procedures.

The filenames above are suggested names. Use the actual filenames in
your project folder.

###  MySQL Workbench

1.  Open MySQL Workbench and connect to your MySQL server.
2.  Select **File → Open SQL Script**.
3.  Choose the required `.sql` file.
4.  Confirm that the script uses the correct database with
    `USE online_shopping_db;`.
5.  Click the lightning-bolt **Execute** button.
6.  Repeat for the remaining files in the order listed above.
7.  Check the **Action Output** panel for execution errors.

## 7. Sample SQL Queries

The following examples demonstrate common operations using the project
tables.

### 7.1 Display all customers

``` sql
SELECT *
FROM Customers;
```

### 7.2 Display products with a price greater than 5,000

``` sql
SELECT product_name, category, price
FROM Products
WHERE price > 5000
ORDER BY price DESC;
```

### 7.3 Display customer names with their order details

``` sql
SELECT
    c.customer_name,
    o.order_id,
    o.order_date,
    o.order_status
FROM Customers AS c
INNER JOIN Orders AS o
    ON c.customer_id = o.customer_id;
```

### 7.4 Calculate the total value of each order

``` sql
SELECT
    o.order_id,
    SUM(oi.quantity * oi.price) AS order_total
FROM Orders AS o
INNER JOIN Order_Items AS oi
    ON o.order_id = oi.order_id
GROUP BY o.order_id
ORDER BY order_total DESC;
```

### 7.5 Find customers who have placed more than one order

``` sql
SELECT
    c.customer_id,
    c.customer_name,
    COUNT(o.order_id) AS total_orders
FROM Customers AS c
INNER JOIN Orders AS o
    ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.customer_name
HAVING COUNT(o.order_id) > 1;
```

### 7.6 Find products priced above the average product price

``` sql
SELECT product_name, price
FROM Products
WHERE price > (
    SELECT AVG(price)
    FROM Products
);
```

### 7.7 Find products that have never been ordered

``` sql
SELECT
    p.product_id,
    p.product_name
FROM Products AS p
WHERE NOT EXISTS (
    SELECT 1
    FROM Order_Items AS oi
    WHERE oi.product_id = p.product_id
);
```

### 7.8 Display payment details with customer names

``` sql
SELECT
    c.customer_name,
    o.order_id,
    p.amount,
    p.payment_method,
    p.payment_status
FROM Customers AS c
INNER JOIN Orders AS o
    ON c.customer_id = o.customer_id
INNER JOIN Payments AS p
    ON o.order_id = p.order_id;
```

### 7.9 Find total completed payment amount

``` sql
SELECT
    SUM(amount) AS total_completed_payments
FROM Payments
WHERE payment_status = 'Completed';
```

### 7.10 Show monthly order counts

``` sql
SELECT
    YEAR(order_date) AS order_year,
    MONTHNAME(order_date) AS order_month,
    COUNT(*) AS total_orders
FROM Orders
GROUP BY YEAR(order_date), MONTH(order_date), MONTHNAME(order_date)
ORDER BY order_year, MONTH(order_date);
```

## 8. Future Enhancements

The project can be extended with the following features:

-   Add a `Categories` table to manage product categories separately.
-   Add an `Address` table to support multiple delivery addresses per
    customer.
-   Add an `Inventory_Transactions` table to maintain stock movement
    history.
-   Add an `Order_Status_History` table to track changes in order
    status.
-   Add discount and coupon management.
-   Add product reviews and ratings.
-   Add support for refunds and multiple payment attempts.
-   Create additional views and stored procedures for business
    reporting.
-   Add triggers to automate selected database operations.
-   Connect the database to a front-end or web application in a future
    version.

## 9. Learning Outcomes

By completing this project, learners can practice:

-   Relational database design and normalization concepts.
-   DDL commands such as `CREATE`, `ALTER`, and `DROP`.
-   DML commands such as `INSERT`, `UPDATE`, and `DELETE`.
-   DQL using filtering, sorting, grouping, and aggregate functions.
-   Joins, subqueries, and correlated subqueries.
-   MySQL built-in functions and conditional expressions.
-   Views and stored procedures.
-   Primary keys, foreign keys, and referential integrity.

## 10. Conclusion

The Online Shopping Management System demonstrates how MySQL can be used
to design and manage a relational database for an e-commerce use case.
With five interconnected tables, sample records, and practical SQL
queries, the project provides hands-on experience in database management
and data analysis.

------------------------------------------------------------------------

**Project Type:** Database Management System\
**Domain:** E-commerce\
**Database:** MySQL\
**Implementation:** SQL only
