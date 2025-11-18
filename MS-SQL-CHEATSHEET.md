# MS SQL Syntax Cheat Sheet
## Microsoft SQL Server - Quick Reference Guide

> **Quick Guide**: This cheat sheet helps you quickly remember MS SQL syntax for common database operations. Each example includes comments showing the expected results!

---

## ⚡ Quick Reference Card

**Most Common Commands** - Copy and paste these!

```sql
-- Read data
SELECT * FROM TableName;
SELECT Column1, Column2 FROM TableName WHERE Column1 = 'value';

-- Filter and sort
WHERE Price > 100 AND Category = 'Electronics'
ORDER BY Price DESC
TOP 10

-- Join tables (combine data from two tables)
SELECT c.Name, o.OrderDate
FROM Customers c
INNER JOIN Orders o ON c.ID = o.CustomerID

-- Add new data
INSERT INTO Customers (Name, Email) VALUES ('John', 'john@email.com');

-- Change existing data
UPDATE Customers SET Email = 'new@email.com' WHERE ID = 5;

-- Remove data
DELETE FROM Customers WHERE ID = 5;

-- Count and sum
SELECT COUNT(*) FROM Customers;
SELECT SUM(Price) FROM Orders;

-- Group data (like Excel pivot)
SELECT Category, COUNT(*) AS Total
FROM Products
GROUP BY Category;

-- Check if value is empty
WHERE Phone IS NULL
WHERE Phone IS NOT NULL

-- Pattern matching (find text)
WHERE Email LIKE '%@gmail.com'  -- Ends with @gmail.com
WHERE Name LIKE 'A%'             -- Starts with A

-- Date queries
WHERE OrderDate >= '2024-01-01'
WHERE OrderDate BETWEEN '2024-01-01' AND '2024-12-31'
```

---

## Table of Contents

### 🟢 Basics (Start Here!)
1. [SELECT - Reading Data](#select---reading-data)
2. [WHERE - Filtering Data](#where---filtering-data)
3. [ORDER BY - Sorting Results](#order-by---sorting-results)
4. [TOP & OFFSET - Limiting Results](#top--offset---limiting-results)
5. [DISTINCT - Removing Duplicates](#distinct---removing-duplicates)

### 🟢 Working with Data
6. [INSERT - Adding Data](#insert---adding-data)
7. [UPDATE - Modifying Data](#update---modifying-data)
8. [DELETE - Removing Data](#delete---removing-data)
9. [Common Data Types](#common-data-types)

### 🟡 Combining Data
10. [INNER JOIN](#inner-join)
11. [LEFT JOIN](#left-join)
12. [RIGHT JOIN & FULL JOIN](#right-join--full-join)
13. [UNION & UNION ALL](#union--union-all)

### 🟡 Aggregation & Grouping
14. [COUNT, SUM, AVG, MIN, MAX](#count-sum-avg-min-max)
15. [GROUP BY](#group-by)
16. [HAVING - Filtering Groups](#having---filtering-groups)

### 🟡 String Functions
17. [String Manipulation](#string-manipulation)
18. [String Searching & Patterns](#string-searching--patterns)

### 🟡 Date & Time
19. [Date Functions](#date-functions)
20. [Date Formatting](#date-formatting)
21. [Date Calculations](#date-calculations)

### 🟡 Conditional Logic
22. [CASE Statements](#case-statements)
23. [IF/ELSE in Queries](#ifelse-in-queries)
24. [NULL Handling](#null-handling)

### 🔴 Creating & Modifying Tables
25. [CREATE TABLE](#create-table)
26. [ALTER TABLE](#alter-table)
27. [DROP & TRUNCATE](#drop--truncate)
28. [Constraints](#constraints)

### 🔴 Indexes & Performance
29. [Indexes](#indexes)
30. [Views](#views)

### 🔴 Advanced Features
31. [Stored Procedures](#stored-procedures)
32. [Functions](#functions)
33. [Transactions](#transactions)
34. [Subqueries](#subqueries)
35. [Common Table Expressions (CTE)](#common-table-expressions-cte)
36. [Window Functions](#window-functions)

### 🔴 More Advanced Features
37. [Temporary Tables & Variables](#temporary-tables--variables)
38. [MERGE Statement](#merge-statement)
39. [OUTPUT Clause](#output-clause)
40. [CROSS APPLY & OUTER APPLY](#cross-apply--outer-apply)
41. [PIVOT & UNPIVOT](#pivot--unpivot)
42. [Dynamic SQL](#dynamic-sql)

### 💡 Practical Patterns
43. [Pagination with Total Count](#pagination-with-total-count)
44. [Upsert (Insert or Update)](#upsert-insert-or-update)
45. [Finding Duplicates](#finding-duplicates)
46. [Deleting Duplicates](#deleting-duplicates)
47. [Running Totals & Cumulative Sums](#running-totals--cumulative-sums)

### 🔐 Security & Performance
48. [SQL Injection Prevention](#sql-injection-prevention)
49. [Performance Best Practices](#performance-best-practices)
50. [Troubleshooting Slow Queries](#troubleshooting-slow-queries)

### 📊 Common Business Scenarios
51. [Inventory Management](#inventory-management)
52. [Sales Reports](#sales-reports)
53. [User Activity Tracking](#user-activity-tracking)

### 📖 Quick References
54. [Type Conversion & Casting](#type-conversion--casting)

### ⚠️ Common Pitfalls
55. [Common Mistakes & Gotchas](#common-mistakes--gotchas)

---

## SELECT - Reading Data

**What it does**: Retrieves data from database tables.

### Basic SELECT

```sql
-- Get all columns from a table
SELECT * FROM Customers;
-- Returns: All rows and all columns from Customers table

-- Get specific columns
SELECT FirstName, LastName, Email FROM Customers;
-- Returns: Only FirstName, LastName, and Email columns

-- Add column aliases for clearer output
SELECT
    FirstName AS Name,          -- Rename column in output
    Email AS ContactEmail
FROM Customers;
-- Returns: Columns with new names "Name" and "ContactEmail"

-- Calculate values
SELECT
    ProductName,
    Price,
    Price * 1.2 AS PriceWithTax -- Calculate on the fly
FROM Products;
-- Returns: ProductName, original Price, and calculated PriceWithTax
```

---

## WHERE - Filtering Data

**What it does**: Filters rows based on conditions.

```sql
-- Simple equality
SELECT * FROM Customers
WHERE Country = 'USA';
-- Returns: Only customers from USA

-- Multiple conditions with AND
SELECT * FROM Products
WHERE Price > 100 AND Category = 'Electronics';
-- Returns: Electronics products over $100

-- Multiple conditions with OR
SELECT * FROM Customers
WHERE Country = 'USA' OR Country = 'Canada';
-- Returns: Customers from USA or Canada

-- Using IN for multiple values
SELECT * FROM Customers
WHERE Country IN ('USA', 'Canada', 'Mexico');
-- Returns: Customers from USA, Canada, or Mexico

-- Range checking with BETWEEN
SELECT * FROM Orders
WHERE OrderDate BETWEEN '2024-01-01' AND '2024-12-31';
-- Returns: All orders in 2024

-- Pattern matching with LIKE
SELECT * FROM Customers
WHERE Email LIKE '%@gmail.com';
-- Returns: All customers with Gmail addresses
-- % means "any characters"

SELECT * FROM Products
WHERE ProductName LIKE 'A%';
-- Returns: Products starting with 'A'

-- IS NULL / IS NOT NULL
SELECT * FROM Customers
WHERE Phone IS NULL;
-- Returns: Customers with no phone number

SELECT * FROM Customers
WHERE Phone IS NOT NULL;
-- Returns: Customers who have a phone number

-- NOT operator
SELECT * FROM Products
WHERE NOT Category = 'Electronics';
-- Returns: All products except Electronics
```

---

## ORDER BY - Sorting Results

**What it does**: Sorts the result set by one or more columns.

```sql
-- Sort ascending (A to Z, smallest to largest)
SELECT * FROM Customers
ORDER BY LastName ASC;
-- Returns: Customers sorted by last name A-Z

-- Sort descending (Z to A, largest to smallest)
SELECT * FROM Products
ORDER BY Price DESC;
-- Returns: Products from most expensive to cheapest

-- Sort by multiple columns
SELECT * FROM Customers
ORDER BY Country ASC, LastName ASC;
-- Returns: Customers sorted by country, then by last name within each country

-- Sort by calculated column
SELECT
    ProductName,
    Price,
    Price * 0.2 AS Tax
FROM Products
ORDER BY Tax DESC;
-- Returns: Products sorted by tax amount (highest tax first)
```

---

## TOP & OFFSET - Limiting Results

**What it does**: Controls how many rows to return.

```sql
-- Get first N rows
SELECT TOP 10 * FROM Customers;
-- Returns: First 10 customers

-- Get top N percent
SELECT TOP 10 PERCENT * FROM Customers
ORDER BY TotalPurchases DESC;
-- Returns: Top 10% of customers by purchases

-- Pagination with OFFSET and FETCH (SQL Server 2012+)
SELECT * FROM Products
ORDER BY ProductName
OFFSET 0 ROWS           -- Skip 0 rows (first page)
FETCH NEXT 20 ROWS ONLY; -- Get 20 rows
-- Returns: First 20 products (page 1)

SELECT * FROM Products
ORDER BY ProductName
OFFSET 20 ROWS          -- Skip first 20 rows (second page)
FETCH NEXT 20 ROWS ONLY; -- Get next 20 rows
-- Returns: Products 21-40 (page 2)

-- TOP with WHERE
SELECT TOP 5 * FROM Products
WHERE Category = 'Electronics'
ORDER BY Price DESC;
-- Returns: 5 most expensive electronics
```

---

## DISTINCT - Removing Duplicates

**What it does**: Returns only unique values.

```sql
-- Get unique values
SELECT DISTINCT Country FROM Customers;
-- Returns: List of countries with no duplicates

-- Multiple columns - unique combinations
SELECT DISTINCT Country, City FROM Customers;
-- Returns: Unique combinations of Country and City

-- Count unique values
SELECT COUNT(DISTINCT Country) AS CountryCount
FROM Customers;
-- Returns: Number of different countries
```

---

## INSERT - Adding Data

**What it does**: Adds new rows to a table.

```sql
-- Insert with all columns specified
INSERT INTO Customers (FirstName, LastName, Email, Country)
VALUES ('John', 'Doe', 'john@email.com', 'USA');
-- Adds: One new customer

-- Insert multiple rows at once
INSERT INTO Customers (FirstName, LastName, Email, Country)
VALUES
    ('Jane', 'Smith', 'jane@email.com', 'Canada'),
    ('Bob', 'Johnson', 'bob@email.com', 'USA'),
    ('Alice', 'Williams', 'alice@email.com', 'UK');
-- Adds: Three new customers

-- Insert from another table
INSERT INTO ArchivedOrders (OrderID, CustomerID, OrderDate)
SELECT OrderID, CustomerID, OrderDate
FROM Orders
WHERE OrderDate < '2020-01-01';
-- Copies: All old orders to archive table

-- Insert with default values
INSERT INTO Products (ProductName, Category)
VALUES ('New Product', 'General');
-- Other columns get their default values or NULL
```

---

## UPDATE - Modifying Data

**What it does**: Changes existing data in a table.

```sql
-- Update single column
UPDATE Customers
SET Country = 'United States'
WHERE Country = 'USA';
-- Changes: Country name for all USA customers

-- Update multiple columns
UPDATE Products
SET
    Price = Price * 1.1,        -- Increase by 10%
    LastUpdated = GETDATE()     -- Set to current date/time
WHERE Category = 'Electronics';
-- Changes: Price and LastUpdated for all electronics

-- Update with calculation
UPDATE Employees
SET Salary = Salary + 5000
WHERE YearsOfService > 5;
-- Adds: $5000 to salary for employees with 5+ years

-- Update from another table
UPDATE o
SET o.ShippedDate = GETDATE()
FROM Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID
WHERE c.Country = 'USA';
-- Updates: ShippedDate for all USA orders

-- ⚠️ WARNING: Without WHERE, updates ALL rows!
UPDATE Products SET Price = 0;  -- DANGER: Sets all prices to 0!
```

---

## DELETE - Removing Data

**What it does**: Removes rows from a table.

```sql
-- Delete with condition
DELETE FROM Customers
WHERE Country = 'TestCountry';
-- Removes: All customers from TestCountry

-- Delete with multiple conditions
DELETE FROM Orders
WHERE OrderDate < '2020-01-01'
  AND Status = 'Cancelled';
-- Removes: Old cancelled orders

-- Delete based on another table
DELETE FROM Orders
WHERE CustomerID IN (
    SELECT CustomerID FROM Customers WHERE IsActive = 0
);
-- Removes: Orders from inactive customers

-- ⚠️ WARNING: Without WHERE, deletes ALL rows!
DELETE FROM Products;  -- DANGER: Deletes everything!

-- Safer: Use SELECT first to verify
SELECT * FROM Orders WHERE OrderDate < '2020-01-01';
-- Check results, then change SELECT to DELETE
DELETE FROM Orders WHERE OrderDate < '2020-01-01';
```

---

## Common Data Types

**What it does**: Defines what kind of data a column can store.

```sql
-- Text types
VARCHAR(50)         -- Variable length text up to 50 characters
NVARCHAR(100)       -- Unicode text (supports all languages)
CHAR(10)            -- Fixed length text (always 10 characters)
TEXT                -- Large text (deprecated, use VARCHAR(MAX))

-- Number types
INT                 -- Whole numbers (-2 billion to +2 billion)
BIGINT              -- Very large whole numbers
DECIMAL(10,2)       -- Numbers with decimals (10 digits, 2 after decimal)
FLOAT               -- Approximate decimal numbers
BIT                 -- True/False (stores as 1/0)

-- Date/Time types
DATE                -- Date only (2024-01-15)
DATETIME            -- Date and time (2024-01-15 14:30:00)
DATETIME2           -- More precise date/time (recommended)
TIME                -- Time only (14:30:00)

-- Other types
UNIQUEIDENTIFIER    -- GUID (e.g., for IDs)
MONEY               -- Currency values
BINARY(n)           -- Binary data

-- Example table with various types
CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    ProductName NVARCHAR(100),
    Price DECIMAL(10,2),
    InStock BIT,
    CreatedDate DATETIME2,
    Description NVARCHAR(MAX)
);
```

---

## INNER JOIN

**What it does**: Combines rows from two tables where there's a match.

```sql
-- Basic INNER JOIN
SELECT
    Customers.FirstName,
    Customers.LastName,
    Orders.OrderDate,
    Orders.TotalAmount
FROM Customers
INNER JOIN Orders ON Customers.CustomerID = Orders.CustomerID;
-- Returns: Only customers who have orders (customers without orders excluded)

-- Using table aliases (shorter and clearer)
SELECT
    c.FirstName,
    c.LastName,
    o.OrderDate,
    o.TotalAmount
FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID;
-- Returns: Same as above, but with shorter syntax

-- Join multiple tables
SELECT
    c.FirstName,
    p.ProductName,
    od.Quantity,
    od.Price
FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID
INNER JOIN OrderDetails od ON o.OrderID = od.OrderID
INNER JOIN Products p ON od.ProductID = p.ProductID;
-- Returns: Customer names with all products they ordered

-- Join with WHERE
SELECT
    c.FirstName,
    o.OrderDate
FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE o.OrderDate >= '2024-01-01';
-- Returns: Customers with their 2024 orders only
```

---

## LEFT JOIN

**What it does**: Returns all rows from left table, and matching rows from right table (NULL if no match).

```sql
-- LEFT JOIN - keep all customers
SELECT
    c.FirstName,
    c.LastName,
    o.OrderID,
    o.OrderDate
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID;
-- Returns: ALL customers, even those without orders
--          Customers without orders show NULL for OrderID and OrderDate

-- Find customers with NO orders
SELECT
    c.FirstName,
    c.LastName
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE o.OrderID IS NULL;
-- Returns: Only customers who never placed an order

-- Count orders per customer (including zero)
SELECT
    c.FirstName,
    c.LastName,
    COUNT(o.OrderID) AS OrderCount
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.FirstName, c.LastName;
-- Returns: Each customer with their order count (0 if no orders)
```

---

## RIGHT JOIN & FULL JOIN

**What it does**: RIGHT JOIN keeps all rows from right table. FULL JOIN keeps all rows from both tables.

```sql
-- RIGHT JOIN (less common, but opposite of LEFT JOIN)
SELECT
    c.FirstName,
    o.OrderID
FROM Customers c
RIGHT JOIN Orders o ON c.CustomerID = o.CustomerID;
-- Returns: All orders, even if customer was deleted
--          (OrderID shows, but FirstName is NULL if customer missing)

-- FULL OUTER JOIN - keeps everything from both tables
SELECT
    c.FirstName,
    o.OrderID
FROM Customers c
FULL OUTER JOIN Orders o ON c.CustomerID = o.CustomerID;
-- Returns: All customers AND all orders
--          NULLs where there's no match on either side

-- Find mismatches with FULL JOIN
SELECT
    c.CustomerID AS CustID,
    o.CustomerID AS OrderCustID
FROM Customers c
FULL OUTER JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE c.CustomerID IS NULL OR o.CustomerID IS NULL;
-- Returns: Customers with no orders OR orders with no customer
```

---

## UNION & UNION ALL

**What it does**: Combines results from multiple queries.

```sql
-- UNION - combines and removes duplicates
SELECT City FROM Customers
UNION
SELECT City FROM Suppliers;
-- Returns: List of all cities (each city appears once)

-- UNION ALL - combines keeping duplicates (faster)
SELECT City FROM Customers
UNION ALL
SELECT City FROM Suppliers;
-- Returns: All cities including duplicates

-- UNION with ORDER BY
SELECT FirstName, LastName, 'Customer' AS Type FROM Customers
UNION
SELECT FirstName, LastName, 'Employee' AS Type FROM Employees
ORDER BY LastName;
-- Returns: Combined list sorted by last name

-- Must have same number and type of columns
SELECT ProductName, Price FROM Products      -- 2 columns
UNION
SELECT CategoryName, NULL FROM Categories;   -- Must also be 2 columns
```

---

## COUNT, SUM, AVG, MIN, MAX

**What it does**: Performs calculations on groups of rows.

```sql
-- COUNT - count rows
SELECT COUNT(*) AS TotalCustomers FROM Customers;
-- Returns: Total number of customers (e.g., 150)

SELECT COUNT(Phone) AS CustomersWithPhone FROM Customers;
-- Returns: Number of customers who have a phone number (ignores NULLs)

-- SUM - add up values
SELECT SUM(TotalAmount) AS TotalRevenue FROM Orders;
-- Returns: Sum of all order amounts (e.g., 125000.00)

-- AVG - average
SELECT AVG(Price) AS AveragePrice FROM Products;
-- Returns: Average product price (e.g., 49.99)

-- MIN and MAX
SELECT
    MIN(Price) AS CheapestProduct,
    MAX(Price) AS MostExpensive
FROM Products;
-- Returns: Lowest and highest prices (e.g., 9.99 and 999.99)

-- Multiple aggregates together
SELECT
    COUNT(*) AS TotalOrders,
    SUM(TotalAmount) AS TotalRevenue,
    AVG(TotalAmount) AS AvgOrderValue,
    MIN(TotalAmount) AS SmallestOrder,
    MAX(TotalAmount) AS LargestOrder
FROM Orders;
-- Returns: All statistics in one row
```

---

## GROUP BY

**What it does**: Groups rows and performs calculations for each group.

```sql
-- Count by category
SELECT
    Category,
    COUNT(*) AS ProductCount
FROM Products
GROUP BY Category;
-- Returns: Each category with its product count
--          Electronics: 25
--          Clothing: 40
--          Books: 30

-- Sum by customer
SELECT
    CustomerID,
    SUM(TotalAmount) AS TotalSpent
FROM Orders
GROUP BY CustomerID;
-- Returns: Each customer with their total spending

-- Multiple columns in GROUP BY
SELECT
    Country,
    City,
    COUNT(*) AS CustomerCount
FROM Customers
GROUP BY Country, City;
-- Returns: Customer count for each city in each country
--          USA, New York: 15
--          USA, Los Angeles: 10
--          Canada, Toronto: 8

-- GROUP BY with JOIN
SELECT
    c.FirstName,
    c.LastName,
    COUNT(o.OrderID) AS OrderCount,
    SUM(o.TotalAmount) AS TotalSpent
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.FirstName, c.LastName;
-- Returns: Each customer with their order count and total spending

-- ⚠️ Must include all non-aggregate columns in GROUP BY
SELECT Category, ProductName, COUNT(*)  -- ERROR!
FROM Products
GROUP BY Category;
-- This fails because ProductName is not in GROUP BY or an aggregate
```

---

## HAVING - Filtering Groups

**What it does**: Filters groups created by GROUP BY (WHERE filters rows before grouping, HAVING filters after).

```sql
-- Filter groups with HAVING
SELECT
    Category,
    COUNT(*) AS ProductCount
FROM Products
GROUP BY Category
HAVING COUNT(*) > 10;
-- Returns: Only categories with more than 10 products

-- HAVING with multiple conditions
SELECT
    CustomerID,
    COUNT(OrderID) AS OrderCount,
    SUM(TotalAmount) AS TotalSpent
FROM Orders
GROUP BY CustomerID
HAVING COUNT(OrderID) >= 5 AND SUM(TotalAmount) > 1000;
-- Returns: Customers with 5+ orders AND spent over $1000

-- WHERE vs HAVING
SELECT
    Category,
    COUNT(*) AS ProductCount
FROM Products
WHERE Price > 50              -- Filter ROWS first (before grouping)
GROUP BY Category
HAVING COUNT(*) > 5;          -- Filter GROUPS after (after grouping)
-- Returns: Categories with 5+ products priced over $50

-- Order of execution:
-- 1. FROM/JOIN - get data
-- 2. WHERE - filter rows
-- 3. GROUP BY - create groups
-- 4. HAVING - filter groups
-- 5. SELECT - calculate results
-- 6. ORDER BY - sort output
```

---

## String Manipulation

**What it does**: Functions for working with text.

```sql
-- Concatenation
SELECT FirstName + ' ' + LastName AS FullName FROM Customers;
-- Returns: "John Doe"

SELECT CONCAT(FirstName, ' ', LastName) AS FullName FROM Customers;
-- Returns: "John Doe" (safer with NULLs)

-- Upper and lower case
SELECT
    UPPER(Email) AS UpperEmail,     -- john@email.com → JOHN@EMAIL.COM
    LOWER(Email) AS LowerEmail      -- JOHN@EMAIL.COM → john@email.com
FROM Customers;

-- Trim whitespace
SELECT TRIM('  hello  ') AS Trimmed;           -- Returns: "hello"
SELECT LTRIM('  hello') AS LeftTrim;           -- Returns: "hello  "
SELECT RTRIM('hello  ') AS RightTrim;          -- Returns: "  hello"

-- Substring
SELECT SUBSTRING('Hello World', 1, 5) AS Sub;  -- Returns: "Hello"
SELECT SUBSTRING(Email, 1, CHARINDEX('@', Email) - 1) AS Username
FROM Customers;
-- Returns: Username part before @

-- Length
SELECT LEN('Hello') AS Length;                 -- Returns: 5
SELECT LEN(ProductName) AS NameLength FROM Products;

-- Replace
SELECT REPLACE(Phone, '-', '') AS PhoneNoHyphens FROM Customers;
-- Returns: "1234567890" instead of "123-456-7890"

-- Left and Right
SELECT LEFT('Hello', 3) AS FirstThree;         -- Returns: "Hel"
SELECT RIGHT('Hello', 3) AS LastThree;         -- Returns: "llo"

-- Reverse
SELECT REVERSE('Hello') AS Backwards;          -- Returns: "olleH"
```

---

## String Searching & Patterns

**What it does**: Find and match text patterns.

```sql
-- LIKE patterns
SELECT * FROM Customers WHERE LastName LIKE 'S%';
-- Returns: Last names starting with S
-- % = any characters (0 or more)

SELECT * FROM Customers WHERE Email LIKE '%@gmail.com';
-- Returns: Gmail users

SELECT * FROM Products WHERE ProductName LIKE '%phone%';
-- Returns: Products with "phone" anywhere in name

SELECT * FROM Customers WHERE LastName LIKE '_mith';
-- Returns: "Smith", "Amith", etc.
-- _ = exactly one character

-- CHARINDEX - find position of substring
SELECT CHARINDEX('@', 'john@email.com') AS Position;
-- Returns: 5 (position of @)

SELECT * FROM Customers
WHERE CHARINDEX('@gmail.com', Email) > 0;
-- Returns: Gmail users (alternative to LIKE)

-- PATINDEX - pattern matching with wildcards
SELECT PATINDEX('%[0-9]%', 'Product123') AS Position;
-- Returns: 8 (position of first number)

-- Multiple patterns
SELECT * FROM Products
WHERE ProductName LIKE '%phone%'
   OR ProductName LIKE '%tablet%';
-- Returns: Products containing "phone" or "tablet"
```

---

## Date Functions

**What it does**: Work with dates and times.

```sql
-- Current date and time
SELECT GETDATE() AS CurrentDateTime;
-- Returns: 2024-11-18 14:30:00.123

SELECT GETUTCDATE() AS UTCTime;
-- Returns: Current time in UTC

-- Get parts of a date
SELECT
    YEAR(OrderDate) AS Year,           -- 2024
    MONTH(OrderDate) AS Month,         -- 11
    DAY(OrderDate) AS Day              -- 18
FROM Orders;

SELECT DATEPART(QUARTER, GETDATE()) AS Quarter;  -- Returns: 4
SELECT DATEPART(WEEK, GETDATE()) AS WeekNumber;  -- Returns: 47

-- Get name of month/day
SELECT DATENAME(MONTH, GETDATE()) AS MonthName;    -- Returns: "November"
SELECT DATENAME(WEEKDAY, GETDATE()) AS DayName;    -- Returns: "Monday"

-- First/last day of month
SELECT DATEFROMPARTS(2024, 11, 1) AS FirstOfMonth; -- Returns: 2024-11-01

-- EOMONTH - end of month
SELECT EOMONTH(GETDATE()) AS LastDayOfMonth;
-- Returns: 2024-11-30
```

---

## Date Formatting

**What it does**: Convert dates to different text formats.

```sql
-- FORMAT function (SQL Server 2012+)
SELECT FORMAT(GETDATE(), 'yyyy-MM-dd') AS ISODate;
-- Returns: "2024-11-18"

SELECT FORMAT(GETDATE(), 'MM/dd/yyyy') AS USDate;
-- Returns: "11/18/2024"

SELECT FORMAT(GETDATE(), 'MMMM dd, yyyy') AS LongDate;
-- Returns: "November 18, 2024"

SELECT FORMAT(GETDATE(), 'hh:mm tt') AS Time12Hour;
-- Returns: "02:30 PM"

-- CONVERT with style codes
SELECT CONVERT(VARCHAR, GETDATE(), 101) AS Style101;  -- MM/dd/yyyy
SELECT CONVERT(VARCHAR, GETDATE(), 103) AS Style103;  -- dd/MM/yyyy
SELECT CONVERT(VARCHAR, GETDATE(), 120) AS Style120;  -- yyyy-MM-dd HH:mm:ss

-- Common style codes:
-- 101: MM/dd/yyyy (USA)
-- 103: dd/MM/yyyy (UK/France)
-- 120: yyyy-MM-dd HH:mm:ss (ISO)
-- 126: yyyy-MM-ddTHH:mm:ss (ISO8601)
```

---

## Date Calculations

**What it does**: Add, subtract, and compare dates.

```sql
-- DATEADD - add/subtract time
SELECT DATEADD(DAY, 7, GETDATE()) AS NextWeek;
-- Returns: Date 7 days from now

SELECT DATEADD(MONTH, -1, GETDATE()) AS LastMonth;
-- Returns: One month ago

SELECT DATEADD(YEAR, 1, OrderDate) AS WarrantyExpires FROM Orders;
-- Returns: Order date + 1 year

-- DATEDIFF - difference between dates
SELECT DATEDIFF(DAY, '2024-01-01', '2024-12-31') AS DaysInYear;
-- Returns: 365

SELECT
    OrderDate,
    DATEDIFF(DAY, OrderDate, GETDATE()) AS DaysSinceOrder
FROM Orders;
-- Returns: How many days ago each order was placed

SELECT
    BirthDate,
    DATEDIFF(YEAR, BirthDate, GETDATE()) AS Age
FROM Customers;
-- Returns: Customer age in years

-- Queries with date ranges
SELECT * FROM Orders
WHERE OrderDate >= DATEADD(MONTH, -3, GETDATE());
-- Returns: Orders from last 3 months

SELECT * FROM Orders
WHERE DATEDIFF(DAY, OrderDate, GETDATE()) <= 30;
-- Returns: Orders from last 30 days
```

---

## CASE Statements

**What it does**: Conditional logic in queries (like if/else).

```sql
-- Simple CASE
SELECT
    ProductName,
    Price,
    CASE
        WHEN Price < 50 THEN 'Cheap'
        WHEN Price < 200 THEN 'Medium'
        ELSE 'Expensive'
    END AS PriceCategory
FROM Products;
-- Returns: Each product with a category based on price

-- CASE with multiple columns
SELECT
    FirstName,
    LastName,
    CASE
        WHEN Country IN ('USA', 'Canada', 'Mexico') THEN 'North America'
        WHEN Country IN ('UK', 'France', 'Germany') THEN 'Europe'
        ELSE 'Other'
    END AS Region
FROM Customers;

-- CASE in WHERE
SELECT * FROM Products
WHERE
    CASE
        WHEN Category = 'Electronics' THEN Price < 1000
        WHEN Category = 'Clothing' THEN Price < 100
        ELSE Price < 50
    END = 1;
-- Returns: Products meeting different price criteria by category

-- CASE in ORDER BY
SELECT ProductName, Price, Category
FROM Products
ORDER BY
    CASE Category
        WHEN 'Electronics' THEN 1
        WHEN 'Clothing' THEN 2
        ELSE 3
    END;
-- Returns: Products sorted with Electronics first, then Clothing, then others

-- CASE with aggregates
SELECT
    COUNT(CASE WHEN Price < 50 THEN 1 END) AS CheapProducts,
    COUNT(CASE WHEN Price >= 50 AND Price < 200 THEN 1 END) AS MediumProducts,
    COUNT(CASE WHEN Price >= 200 THEN 1 END) AS ExpensiveProducts
FROM Products;
-- Returns: Count of products in each price range
```

---

## IF/ELSE in Queries

**What it does**: Conditional execution in scripts and procedures.

```sql
-- IF statement
IF EXISTS (SELECT 1 FROM Customers WHERE Email = 'test@email.com')
BEGIN
    PRINT 'Customer exists';
END
ELSE
BEGIN
    PRINT 'Customer not found';
END

-- IF with variables
DECLARE @OrderCount INT;
SELECT @OrderCount = COUNT(*) FROM Orders WHERE CustomerID = 1;

IF @OrderCount > 10
BEGIN
    PRINT 'Valued customer';
    UPDATE Customers SET DiscountLevel = 'Gold' WHERE CustomerID = 1;
END

-- IIF function (inline if, SQL Server 2012+)
SELECT
    ProductName,
    Price,
    IIF(Price > 100, 'Expensive', 'Affordable') AS Category
FROM Products;
-- Returns: Simple two-option categorization
```

---

## NULL Handling

**What it does**: Deal with missing/unknown values.

```sql
-- ISNULL - replace NULL with a value
SELECT
    FirstName,
    ISNULL(Phone, 'No phone') AS Phone
FROM Customers;
-- Returns: "No phone" instead of NULL

-- COALESCE - return first non-NULL value
SELECT
    FirstName,
    COALESCE(Phone, Mobile, 'No contact') AS ContactNumber
FROM Customers;
-- Returns: Phone if available, otherwise Mobile, otherwise "No contact"

-- NULLIF - return NULL if two values match
SELECT NULLIF(Price, 0) AS Price FROM Products;
-- Returns: NULL instead of 0 (useful for division)

-- Checking for NULL
SELECT * FROM Customers WHERE Phone IS NULL;
SELECT * FROM Customers WHERE Phone IS NOT NULL;

-- ⚠️ Never use = NULL or != NULL
SELECT * FROM Customers WHERE Phone = NULL;   -- WRONG! Returns nothing
SELECT * FROM Customers WHERE Phone IS NULL;  -- CORRECT!

-- Aggregate functions ignore NULL
SELECT AVG(Price) FROM Products;
-- Calculates average of non-NULL prices only

-- COUNT(*) vs COUNT(column)
SELECT
    COUNT(*) AS TotalRows,              -- Counts all rows
    COUNT(Phone) AS RowsWithPhone       -- Counts non-NULL values
FROM Customers;
```

---

## CREATE TABLE

**What it does**: Creates a new table in the database.

```sql
-- Basic table
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    FirstName NVARCHAR(50) NOT NULL,
    LastName NVARCHAR(50) NOT NULL,
    Email NVARCHAR(100),
    Phone NVARCHAR(20),
    CreatedDate DATETIME DEFAULT GETDATE()
);

-- Table with identity (auto-increment)
CREATE TABLE Products (
    ProductID INT IDENTITY(1,1) PRIMARY KEY,  -- Auto-increment starting at 1
    ProductName NVARCHAR(100) NOT NULL,
    Price DECIMAL(10,2) NOT NULL,
    Category NVARCHAR(50),
    InStock BIT DEFAULT 1
);

-- Table with foreign key
CREATE TABLE Orders (
    OrderID INT IDENTITY(1,1) PRIMARY KEY,
    CustomerID INT NOT NULL,
    OrderDate DATETIME DEFAULT GETDATE(),
    TotalAmount DECIMAL(10,2),
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

-- Table with multiple constraints
CREATE TABLE Employees (
    EmployeeID INT IDENTITY(1,1) PRIMARY KEY,
    Email NVARCHAR(100) UNIQUE NOT NULL,        -- Must be unique
    Salary DECIMAL(10,2) CHECK (Salary > 0),    -- Must be positive
    DepartmentID INT,
    HireDate DATE DEFAULT GETDATE(),
    CONSTRAINT FK_Dept FOREIGN KEY (DepartmentID)
        REFERENCES Departments(DepartmentID)
);

-- Create table from query results
SELECT * INTO CustomerBackup FROM Customers;
-- Creates new table with all data from Customers

SELECT FirstName, LastName INTO CustomerNames
FROM Customers
WHERE Country = 'USA';
-- Creates new table with filtered data
```

---

## ALTER TABLE

**What it does**: Modifies an existing table structure.

```sql
-- Add column
ALTER TABLE Customers
ADD DateOfBirth DATE;

ALTER TABLE Customers
ADD MiddleName NVARCHAR(50) NULL;

-- Add column with default
ALTER TABLE Products
ADD IsActive BIT NOT NULL DEFAULT 1;

-- Drop column
ALTER TABLE Customers
DROP COLUMN MiddleName;

-- Modify column type
ALTER TABLE Products
ALTER COLUMN Price DECIMAL(12,2);

-- Add constraint
ALTER TABLE Products
ADD CONSTRAINT CHK_Price CHECK (Price >= 0);

ALTER TABLE Orders
ADD CONSTRAINT FK_Customer
FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID);

-- Drop constraint
ALTER TABLE Products
DROP CONSTRAINT CHK_Price;

-- Rename column (using sp_rename)
EXEC sp_rename 'Customers.Phone', 'PhoneNumber', 'COLUMN';
```

---

## DROP & TRUNCATE

**What it does**: Remove tables or data.

```sql
-- DROP TABLE - deletes entire table (structure and data)
DROP TABLE CustomerBackup;
-- ⚠️ Table is completely removed!

-- DROP IF EXISTS (SQL Server 2016+)
DROP TABLE IF EXISTS TempData;
-- Safer: doesn't error if table doesn't exist

-- TRUNCATE - removes all rows but keeps table structure
TRUNCATE TABLE OrdersArchive;
-- ⚠️ All data deleted, but table remains
-- Faster than DELETE, resets IDENTITY counter
-- Cannot use WHERE clause

-- DELETE vs TRUNCATE
DELETE FROM Orders WHERE OrderDate < '2020-01-01';  -- Can filter with WHERE
TRUNCATE TABLE OrdersTemp;                          -- Removes ALL rows (faster)

-- Drop with dependencies
-- Must drop foreign keys first, or use CASCADE
DROP TABLE Customers;  -- Might fail if Orders references it
```

---

## Constraints

**What it does**: Rules that enforce data integrity.

```sql
-- PRIMARY KEY - unique identifier
CREATE TABLE Products (
    ProductID INT PRIMARY KEY,  -- Can only have one primary key
    ProductName NVARCHAR(100)
);

-- UNIQUE - no duplicates allowed
CREATE TABLE Users (
    UserID INT PRIMARY KEY,
    Email NVARCHAR(100) UNIQUE,  -- Each email must be unique
    Username NVARCHAR(50) UNIQUE
);

-- NOT NULL - value required
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,
    FirstName NVARCHAR(50) NOT NULL,  -- Must provide a value
    LastName NVARCHAR(50) NOT NULL,
    Phone NVARCHAR(20)  -- Optional (can be NULL)
);

-- DEFAULT - automatic value if none provided
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    OrderDate DATETIME DEFAULT GETDATE(),
    Status NVARCHAR(20) DEFAULT 'Pending'
);

-- CHECK - custom validation
CREATE TABLE Products (
    ProductID INT PRIMARY KEY,
    Price DECIMAL(10,2) CHECK (Price > 0),
    Discount DECIMAL(3,2) CHECK (Discount BETWEEN 0 AND 1),
    Stock INT CHECK (Stock >= 0)
);

-- FOREIGN KEY - relationship between tables
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,
    CustomerID INT,
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID)
);

-- Named constraints (easier to manage)
CREATE TABLE Products (
    ProductID INT,
    Price DECIMAL(10,2),
    Stock INT,
    CONSTRAINT PK_Products PRIMARY KEY (ProductID),
    CONSTRAINT CHK_Price CHECK (Price > 0),
    CONSTRAINT CHK_Stock CHECK (Stock >= 0)
);
```

---

## Indexes

**What it does**: Speed up queries by creating fast lookup structures.

```sql
-- Create index on single column
CREATE INDEX IX_Customers_LastName
ON Customers(LastName);
-- Makes: SELECT * FROM Customers WHERE LastName = 'Smith' faster

-- Create index on multiple columns
CREATE INDEX IX_Orders_CustomerDate
ON Orders(CustomerID, OrderDate);
-- Makes: Queries filtering by customer and date faster

-- Unique index
CREATE UNIQUE INDEX IX_Customers_Email
ON Customers(Email);
-- Ensures: Email is unique AND speeds up email lookups

-- Index with included columns
CREATE INDEX IX_Orders_CustomerID
ON Orders(CustomerID)
INCLUDE (OrderDate, TotalAmount);
-- Speeds up queries that select OrderDate and TotalAmount with CustomerID

-- Drop index
DROP INDEX IX_Customers_LastName ON Customers;

-- When to use indexes:
-- ✅ Columns in WHERE clauses
-- ✅ Columns in JOIN conditions
-- ✅ Columns in ORDER BY
-- ❌ Small tables (overhead not worth it)
-- ❌ Columns that change frequently (slow down INSERT/UPDATE)
-- ❌ Too many indexes (diminishing returns)

-- View table indexes
EXEC sp_helpindex 'Customers';
```

---

## Views

**What it does**: Saved queries that act like virtual tables.

```sql
-- Create simple view
CREATE VIEW CustomerOrders AS
SELECT
    c.CustomerID,
    c.FirstName,
    c.LastName,
    COUNT(o.OrderID) AS OrderCount,
    SUM(o.TotalAmount) AS TotalSpent
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.FirstName, c.LastName;

-- Use view like a table
SELECT * FROM CustomerOrders;
SELECT * FROM CustomerOrders WHERE OrderCount > 5;

-- Create view with complex logic
CREATE VIEW HighValueCustomers AS
SELECT
    c.CustomerID,
    c.FirstName,
    c.LastName,
    c.Email,
    SUM(o.TotalAmount) AS TotalSpent
FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.FirstName, c.LastName, c.Email
HAVING SUM(o.TotalAmount) > 10000;

-- Modify view
ALTER VIEW CustomerOrders AS
SELECT
    c.CustomerID,
    c.FirstName + ' ' + c.LastName AS FullName,
    COUNT(o.OrderID) AS OrderCount
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.FirstName, c.LastName;

-- Drop view
DROP VIEW CustomerOrders;

-- Benefits of views:
-- ✅ Simplify complex queries
-- ✅ Provide security (show only certain columns)
-- ✅ Consistency (same logic everywhere)
-- ❌ Can be slow if view is complex
```

---

## Stored Procedures

**What it does**: Saved SQL code that can be executed with parameters.

```sql
-- Simple stored procedure
CREATE PROCEDURE GetAllCustomers
AS
BEGIN
    SELECT * FROM Customers;
END;

-- Execute procedure
EXEC GetAllCustomers;

-- Procedure with parameters
CREATE PROCEDURE GetCustomerOrders
    @CustomerID INT
AS
BEGIN
    SELECT * FROM Orders
    WHERE CustomerID = @CustomerID;
END;

-- Execute with parameter
EXEC GetCustomerOrders @CustomerID = 5;

-- Procedure with multiple parameters and default values
CREATE PROCEDURE SearchProducts
    @Category NVARCHAR(50) = NULL,
    @MinPrice DECIMAL(10,2) = 0,
    @MaxPrice DECIMAL(10,2) = 999999
AS
BEGIN
    SELECT * FROM Products
    WHERE
        (@Category IS NULL OR Category = @Category)
        AND Price BETWEEN @MinPrice AND @MaxPrice;
END;

-- Execute with different parameters
EXEC SearchProducts @Category = 'Electronics';
EXEC SearchProducts @MinPrice = 100, @MaxPrice = 500;

-- Procedure with output parameter
CREATE PROCEDURE GetCustomerOrderCount
    @CustomerID INT,
    @OrderCount INT OUTPUT
AS
BEGIN
    SELECT @OrderCount = COUNT(*)
    FROM Orders
    WHERE CustomerID = @CustomerID;
END;

-- Use output parameter
DECLARE @Count INT;
EXEC GetCustomerOrderCount @CustomerID = 5, @OrderCount = @Count OUTPUT;
PRINT @Count;

-- Procedure with INSERT/UPDATE/DELETE
CREATE PROCEDURE AddCustomer
    @FirstName NVARCHAR(50),
    @LastName NVARCHAR(50),
    @Email NVARCHAR(100)
AS
BEGIN
    INSERT INTO Customers (FirstName, LastName, Email)
    VALUES (@FirstName, @LastName, @Email);

    SELECT SCOPE_IDENTITY() AS NewCustomerID;  -- Return new ID
END;

-- Drop procedure
DROP PROCEDURE GetAllCustomers;
```

---

## Functions

**What it does**: Returns a value or table, can be used in queries.

```sql
-- Scalar function (returns single value)
CREATE FUNCTION GetCustomerFullName (@CustomerID INT)
RETURNS NVARCHAR(100)
AS
BEGIN
    DECLARE @FullName NVARCHAR(100);

    SELECT @FullName = FirstName + ' ' + LastName
    FROM Customers
    WHERE CustomerID = @CustomerID;

    RETURN @FullName;
END;

-- Use scalar function
SELECT dbo.GetCustomerFullName(5) AS CustomerName;

SELECT
    OrderID,
    dbo.GetCustomerFullName(CustomerID) AS CustomerName,
    OrderDate
FROM Orders;

-- Function with calculation
CREATE FUNCTION CalculateTax (@Amount DECIMAL(10,2), @TaxRate DECIMAL(3,2))
RETURNS DECIMAL(10,2)
AS
BEGIN
    RETURN @Amount * @TaxRate;
END;

-- Use in query
SELECT
    ProductName,
    Price,
    dbo.CalculateTax(Price, 0.08) AS Tax,
    Price + dbo.CalculateTax(Price, 0.08) AS TotalPrice
FROM Products;

-- Table-valued function (returns table)
CREATE FUNCTION GetCustomerOrders (@CustomerID INT)
RETURNS TABLE
AS
RETURN
(
    SELECT
        OrderID,
        OrderDate,
        TotalAmount
    FROM Orders
    WHERE CustomerID = @CustomerID
);

-- Use table function
SELECT * FROM dbo.GetCustomerOrders(5);

-- Join with table function
SELECT
    c.FirstName,
    c.LastName,
    o.OrderDate,
    o.TotalAmount
FROM Customers c
CROSS APPLY dbo.GetCustomerOrders(c.CustomerID) o;

-- Drop function
DROP FUNCTION GetCustomerFullName;
```

---

## Transactions

**What it does**: Group multiple operations - all succeed or all fail together.

```sql
-- Basic transaction
BEGIN TRANSACTION;

    UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;
    UPDATE Accounts SET Balance = Balance + 100 WHERE AccountID = 2;

COMMIT TRANSACTION;  -- Save changes

-- Transaction with rollback on error
BEGIN TRANSACTION;

    UPDATE Products SET Stock = Stock - 10 WHERE ProductID = 1;

    IF @@ERROR != 0
    BEGIN
        ROLLBACK TRANSACTION;  -- Undo everything
        PRINT 'Error occurred, transaction rolled back';
    END
    ELSE
    BEGIN
        COMMIT TRANSACTION;  -- Save changes
        PRINT 'Transaction successful';
    END

-- Transaction with TRY/CATCH
BEGIN TRY
    BEGIN TRANSACTION;

        INSERT INTO Orders (CustomerID, OrderDate, TotalAmount)
        VALUES (1, GETDATE(), 500);

        UPDATE Customers SET TotalSpent = TotalSpent + 500 WHERE CustomerID = 1;

    COMMIT TRANSACTION;
    PRINT 'Order created successfully';
END TRY
BEGIN CATCH
    ROLLBACK TRANSACTION;
    PRINT 'Error: ' + ERROR_MESSAGE();
END CATCH;

-- Savepoints (partial rollback)
BEGIN TRANSACTION;

    UPDATE Products SET Price = Price * 1.1 WHERE Category = 'Electronics';

    SAVE TRANSACTION SavePoint1;

    UPDATE Products SET Price = Price * 1.2 WHERE Category = 'Clothing';

    -- Oops, clothing increase was wrong
    ROLLBACK TRANSACTION SavePoint1;  -- Only undo clothing update

COMMIT TRANSACTION;  -- Keep electronics update

-- ⚠️ Always use TRY/CATCH with transactions to prevent data corruption
```

---

## Subqueries

**What it does**: Query within another query.

```sql
-- Subquery in WHERE
SELECT * FROM Products
WHERE Price > (SELECT AVG(Price) FROM Products);
-- Returns: Products more expensive than average

-- Subquery with IN
SELECT * FROM Customers
WHERE CustomerID IN (
    SELECT CustomerID FROM Orders WHERE OrderDate >= '2024-01-01'
);
-- Returns: Customers who ordered in 2024

-- Subquery with NOT IN
SELECT * FROM Products
WHERE ProductID NOT IN (
    SELECT ProductID FROM OrderDetails
);
-- Returns: Products that were never ordered

-- Subquery with EXISTS
SELECT * FROM Customers c
WHERE EXISTS (
    SELECT 1 FROM Orders o
    WHERE o.CustomerID = c.CustomerID AND o.TotalAmount > 1000
);
-- Returns: Customers who have at least one order over $1000

-- Subquery with NOT EXISTS
SELECT * FROM Customers c
WHERE NOT EXISTS (
    SELECT 1 FROM Orders o WHERE o.CustomerID = c.CustomerID
);
-- Returns: Customers with no orders

-- Subquery in SELECT (scalar subquery)
SELECT
    ProductName,
    Price,
    (SELECT AVG(Price) FROM Products) AS AvgPrice,
    Price - (SELECT AVG(Price) FROM Products) AS PriceDifference
FROM Products;
-- Returns: Each product with comparison to average

-- Correlated subquery
SELECT
    c.FirstName,
    c.LastName,
    (SELECT COUNT(*) FROM Orders o WHERE o.CustomerID = c.CustomerID) AS OrderCount
FROM Customers c;
-- Returns: Each customer with their order count
```

---

## Common Table Expressions (CTE)

**What it does**: Temporary named result set, makes complex queries easier to read.

```sql
-- Basic CTE
WITH CustomerOrders AS (
    SELECT
        CustomerID,
        COUNT(*) AS OrderCount,
        SUM(TotalAmount) AS TotalSpent
    FROM Orders
    GROUP BY CustomerID
)
SELECT
    c.FirstName,
    c.LastName,
    co.OrderCount,
    co.TotalSpent
FROM Customers c
INNER JOIN CustomerOrders co ON c.CustomerID = co.CustomerID;

-- Multiple CTEs
WITH
    HighValueOrders AS (
        SELECT CustomerID, OrderID, TotalAmount
        FROM Orders
        WHERE TotalAmount > 500
    ),
    CustomerStats AS (
        SELECT
            CustomerID,
            COUNT(*) AS HighValueOrderCount,
            SUM(TotalAmount) AS HighValueTotal
        FROM HighValueOrders
        GROUP BY CustomerID
    )
SELECT
    c.FirstName,
    c.LastName,
    cs.HighValueOrderCount,
    cs.HighValueTotal
FROM Customers c
INNER JOIN CustomerStats cs ON c.CustomerID = cs.CustomerID;

-- Recursive CTE (e.g., organizational hierarchy)
WITH EmployeeHierarchy AS (
    -- Base case: top-level managers
    SELECT EmployeeID, ManagerID, FirstName, 0 AS Level
    FROM Employees
    WHERE ManagerID IS NULL

    UNION ALL

    -- Recursive case: employees with managers
    SELECT e.EmployeeID, e.ManagerID, e.FirstName, eh.Level + 1
    FROM Employees e
    INNER JOIN EmployeeHierarchy eh ON e.ManagerID = eh.EmployeeID
)
SELECT * FROM EmployeeHierarchy;
-- Returns: All employees with their hierarchy level

-- CTE vs Subquery - CTE is more readable
-- Subquery version (harder to read)
SELECT c.FirstName, c.LastName, sub.OrderCount
FROM Customers c
INNER JOIN (
    SELECT CustomerID, COUNT(*) AS OrderCount
    FROM Orders
    GROUP BY CustomerID
) sub ON c.CustomerID = sub.CustomerID;

-- CTE version (easier to read)
WITH CustomerOrders AS (
    SELECT CustomerID, COUNT(*) AS OrderCount
    FROM Orders
    GROUP BY CustomerID
)
SELECT c.FirstName, c.LastName, co.OrderCount
FROM Customers c
INNER JOIN CustomerOrders co ON c.CustomerID = co.CustomerID;
```

---

## Window Functions

**What it does**: Perform calculations across rows related to current row without grouping.

```sql
-- ROW_NUMBER - assign sequential number
SELECT
    ProductName,
    Category,
    Price,
    ROW_NUMBER() OVER (ORDER BY Price DESC) AS RowNum
FROM Products;
-- Returns: Each product with row number (1, 2, 3...)

-- ROW_NUMBER partitioned by category
SELECT
    ProductName,
    Category,
    Price,
    ROW_NUMBER() OVER (PARTITION BY Category ORDER BY Price DESC) AS RankInCategory
FROM Products;
-- Returns: Rank within each category (resets to 1 for each category)

-- RANK and DENSE_RANK
SELECT
    ProductName,
    Price,
    RANK() OVER (ORDER BY Price DESC) AS Rank,
    DENSE_RANK() OVER (ORDER BY Price DESC) AS DenseRank
FROM Products;
-- RANK: 1, 2, 2, 4 (gaps after ties)
-- DENSE_RANK: 1, 2, 2, 3 (no gaps)

-- Running total with SUM window function
SELECT
    OrderDate,
    TotalAmount,
    SUM(TotalAmount) OVER (ORDER BY OrderDate) AS RunningTotal
FROM Orders
ORDER BY OrderDate;
-- Returns: Cumulative sum of orders

-- Average by partition
SELECT
    ProductName,
    Category,
    Price,
    AVG(Price) OVER (PARTITION BY Category) AS CategoryAvgPrice,
    Price - AVG(Price) OVER (PARTITION BY Category) AS PriceDifference
FROM Products;
-- Returns: Each product compared to category average

-- LAG and LEAD (compare to previous/next row)
SELECT
    OrderDate,
    TotalAmount,
    LAG(TotalAmount) OVER (ORDER BY OrderDate) AS PreviousOrderAmount,
    LEAD(TotalAmount) OVER (ORDER BY OrderDate) AS NextOrderAmount
FROM Orders;
-- Returns: Current order with previous and next order amounts

-- FIRST_VALUE and LAST_VALUE
SELECT
    ProductName,
    Category,
    Price,
    FIRST_VALUE(ProductName) OVER (PARTITION BY Category ORDER BY Price DESC) AS MostExpensiveInCategory,
    LAST_VALUE(ProductName) OVER (PARTITION BY Category ORDER BY Price DESC
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS CheapestInCategory
FROM Products;

-- Top N per group using ROW_NUMBER
WITH RankedProducts AS (
    SELECT
        ProductName,
        Category,
        Price,
        ROW_NUMBER() OVER (PARTITION BY Category ORDER BY Price DESC) AS Rank
    FROM Products
)
SELECT ProductName, Category, Price
FROM RankedProducts
WHERE Rank <= 3;
-- Returns: Top 3 most expensive products in each category
```

---

## Temporary Tables & Variables

**What it does**: Store data temporarily while your query or procedure runs (like scratch paper for SQL).

```sql
-- DECLARE variables (store single values)
DECLARE @CustomerName NVARCHAR(100);  -- Text variable
DECLARE @OrderCount INT;              -- Number variable
DECLARE @TotalPrice DECIMAL(10,2);    -- Decimal variable

-- Set variable values
SET @CustomerName = 'John Doe';
SET @OrderCount = 5;

-- Or use SELECT to set from query
SELECT @OrderCount = COUNT(*) FROM Orders WHERE CustomerID = 1;

-- Use variables in queries
SELECT * FROM Customers WHERE FirstName = @CustomerName;
PRINT 'Total orders: ' + CAST(@OrderCount AS VARCHAR);

-- Temporary table (starts with #, exists only in your session)
CREATE TABLE #TempOrders (
    OrderID INT,
    CustomerName NVARCHAR(100),
    TotalAmount DECIMAL(10,2)
);

-- Add data to temp table
INSERT INTO #TempOrders
SELECT OrderID, c.FirstName, TotalAmount
FROM Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID;

-- Use temp table like a regular table
SELECT * FROM #TempOrders WHERE TotalAmount > 100;

-- Temp table is automatically deleted when your session ends
-- Or manually drop it
DROP TABLE #TempOrders;

-- Global temp table (starts with ##, shared across all sessions)
CREATE TABLE ##SharedData (
    ID INT,
    Value NVARCHAR(100)
);
-- Other users can access ##SharedData too
-- Deleted when last session using it closes

-- Table variable (like temp table but stored in memory, faster for small data)
DECLARE @OrdersTable TABLE (
    OrderID INT,
    CustomerID INT,
    TotalAmount DECIMAL(10,2)
);

-- Insert data
INSERT INTO @OrdersTable
SELECT OrderID, CustomerID, TotalAmount FROM Orders WHERE OrderDate >= '2024-01-01';

-- Query it
SELECT * FROM @OrdersTable WHERE TotalAmount > 500;

-- ⚡ When to use what:
-- Variables: Single values (name, count, total)
-- Temp tables (#): Many rows, complex operations, need indexes
-- Table variables (@): Few rows (<1000), simple operations, faster
```

---

## MERGE Statement

**What it does**: Insert, update, or delete in ONE statement (also called "upsert" - update or insert).

```sql
-- Simple explanation: If record exists, update it. If not, insert it.

-- Example: Update product prices or add new products
MERGE INTO Products AS Target                    -- Table we're updating
USING NewProductPrices AS Source                 -- Table with new data
ON Target.ProductID = Source.ProductID           -- How to match records

-- When we find a match, update the price
WHEN MATCHED THEN
    UPDATE SET Target.Price = Source.Price

-- When no match (new product), insert it
WHEN NOT MATCHED BY TARGET THEN
    INSERT (ProductID, ProductName, Price)
    VALUES (Source.ProductID, Source.ProductName, Source.Price)

-- Optional: Delete products not in source
WHEN NOT MATCHED BY SOURCE THEN
    DELETE;

-- Real-world example: Sync customer data
MERGE INTO Customers AS Target
USING ImportedCustomers AS Source
ON Target.Email = Source.Email

WHEN MATCHED THEN
    UPDATE SET
        Target.FirstName = Source.FirstName,
        Target.LastName = Source.LastName,
        Target.Phone = Source.Phone

WHEN NOT MATCHED BY TARGET THEN
    INSERT (Email, FirstName, LastName, Phone)
    VALUES (Source.Email, Source.FirstName, Source.LastName, Source.Phone);

-- Simple upsert example with values
MERGE INTO Products AS Target
USING (SELECT 1 AS ProductID, 'Laptop' AS Name, 999.99 AS Price) AS Source
ON Target.ProductID = Source.ProductID

WHEN MATCHED THEN
    UPDATE SET Target.Price = Source.Price
WHEN NOT MATCHED THEN
    INSERT (ProductID, ProductName, Price)
    VALUES (Source.ProductID, Source.Name, Source.Price);
-- If ProductID 1 exists, updates price. If not, inserts new product.
```

---

## OUTPUT Clause

**What it does**: Shows you what was inserted, updated, or deleted (very useful for debugging and getting IDs).

```sql
-- See what was inserted
INSERT INTO Customers (FirstName, LastName, Email)
OUTPUT INSERTED.CustomerID, INSERTED.FirstName, INSERTED.LastName
VALUES ('Jane', 'Doe', 'jane@email.com');
-- Returns: The new CustomerID and name that was just inserted

-- Save inserted IDs to a table
DECLARE @NewIDs TABLE (CustomerID INT, Email NVARCHAR(100));

INSERT INTO Customers (FirstName, LastName, Email)
OUTPUT INSERTED.CustomerID, INSERTED.Email INTO @NewIDs
VALUES
    ('John', 'Smith', 'john@email.com'),
    ('Mary', 'Johnson', 'mary@email.com');

-- Now you can use the new IDs
SELECT * FROM @NewIDs;

-- See what was updated (shows old and new values)
UPDATE Products
SET Price = Price * 1.1  -- Increase price by 10%
OUTPUT
    DELETED.ProductID,       -- DELETED = old values
    DELETED.Price AS OldPrice,
    INSERTED.Price AS NewPrice  -- INSERTED = new values
WHERE Category = 'Electronics';
-- Returns: ProductID with old and new prices for each updated product

-- See what was deleted
DELETE FROM Orders
OUTPUT
    DELETED.OrderID,
    DELETED.CustomerID,
    DELETED.TotalAmount
WHERE OrderDate < '2020-01-01';
-- Returns: All the orders that were deleted

-- Practical example: Archive deleted records
DECLARE @ArchivedOrders TABLE (
    OrderID INT,
    CustomerID INT,
    OrderDate DATETIME,
    TotalAmount DECIMAL(10,2)
);

DELETE FROM Orders
OUTPUT
    DELETED.OrderID,
    DELETED.CustomerID,
    DELETED.OrderDate,
    DELETED.TotalAmount
INTO @ArchivedOrders
WHERE OrderDate < '2020-01-01';

-- Now @ArchivedOrders contains all deleted orders
SELECT * FROM @ArchivedOrders;
```

---

## CROSS APPLY & OUTER APPLY

**What it does**: Like a JOIN but can use a function or subquery that references each row (think of it as a "for each row" operation).

```sql
-- Simple explanation:
-- CROSS APPLY = INNER JOIN (only rows with matches)
-- OUTER APPLY = LEFT JOIN (all rows, even without matches)

-- Example: Get top 3 orders for each customer

-- Using CROSS APPLY (only customers with orders)
SELECT
    c.FirstName,
    c.LastName,
    TopOrders.OrderID,
    TopOrders.OrderDate,
    TopOrders.TotalAmount
FROM Customers c
CROSS APPLY (
    SELECT TOP 3 OrderID, OrderDate, TotalAmount
    FROM Orders o
    WHERE o.CustomerID = c.CustomerID  -- For THIS customer
    ORDER BY OrderDate DESC
) AS TopOrders;
-- Returns: Each customer with their 3 most recent orders

-- Using OUTER APPLY (includes customers with no orders)
SELECT
    c.FirstName,
    c.LastName,
    TopOrders.OrderID,
    TopOrders.OrderDate,
    TopOrders.TotalAmount
FROM Customers c
OUTER APPLY (
    SELECT TOP 3 OrderID, OrderDate, TotalAmount
    FROM Orders o
    WHERE o.CustomerID = c.CustomerID
    ORDER BY OrderDate DESC
) AS TopOrders;
-- Returns: ALL customers, with their orders if they have any (NULL if no orders)

-- Practical example: Split comma-separated values
DECLARE @Data TABLE (ID INT, Tags NVARCHAR(100));
INSERT INTO @Data VALUES
    (1, 'red,blue,green'),
    (2, 'yellow,orange'),
    (3, 'purple');

SELECT
    d.ID,
    Tag.value AS Tag
FROM @Data d
CROSS APPLY STRING_SPLIT(d.Tags, ',') AS Tag;
-- Returns:
-- 1, red
-- 1, blue
-- 1, green
-- 2, yellow
-- 2, orange
-- 3, purple

-- Using a table-valued function
CREATE FUNCTION GetCustomerOrders(@CustomerID INT)
RETURNS TABLE AS RETURN
    SELECT OrderID, OrderDate, TotalAmount
    FROM Orders
    WHERE CustomerID = @CustomerID;
GO

SELECT
    c.FirstName,
    o.OrderID,
    o.TotalAmount
FROM Customers c
CROSS APPLY dbo.GetCustomerOrders(c.CustomerID) o;
-- Returns: All customers with their orders
```

---

## PIVOT & UNPIVOT

**What it does**: PIVOT turns rows into columns (like Excel pivot table). UNPIVOT does the opposite.

```sql
-- PIVOT - turn rows into columns
-- Example: Sales by month as columns

-- Sample data:
-- Month   | SalesAmount
-- January | 1000
-- February| 1500
-- March   | 2000

SELECT *
FROM (
    SELECT Month, SalesAmount FROM MonthlySales
) AS SourceTable
PIVOT (
    SUM(SalesAmount)           -- What to calculate
    FOR Month                  -- Column to spread out
    IN ([January], [February], [March])  -- New column names
) AS PivotTable;
-- Result:
-- January | February | March
-- 1000    | 1500     | 2000

-- Practical example: Product sales by category
SELECT *
FROM (
    SELECT Category, ProductName, Price
    FROM Products
) AS SourceTable
PIVOT (
    COUNT(ProductName)
    FOR Category
    IN ([Electronics], [Clothing], [Books])
) AS PivotTable;
-- Returns: Count of products in each category as columns

-- UNPIVOT - turn columns back into rows
-- Example: Quarterly sales as columns → rows

-- Sample data:
-- Product | Q1   | Q2   | Q3   | Q4
-- Laptop  | 1000 | 1500 | 2000 | 2500

SELECT Product, Quarter, Sales
FROM QuarterlySales
UNPIVOT (
    Sales               -- New column for values
    FOR Quarter         -- New column for old column names
    IN (Q1, Q2, Q3, Q4) -- Columns to unpivot
) AS UnpivotTable;
-- Result:
-- Laptop | Q1 | 1000
-- Laptop | Q2 | 1500
-- Laptop | Q3 | 2000
-- Laptop | Q4 | 2500

-- Real-world example: Monthly comparison
SELECT
    ProductName,
    [January] AS Jan,
    [February] AS Feb,
    [March] AS Mar
FROM (
    SELECT
        p.ProductName,
        DATENAME(MONTH, o.OrderDate) AS Month,
        od.Quantity
    FROM Products p
    INNER JOIN OrderDetails od ON p.ProductID = od.ProductID
    INNER JOIN Orders o ON od.OrderID = o.OrderID
    WHERE YEAR(o.OrderDate) = 2024
) AS SourceData
PIVOT (
    SUM(Quantity)
    FOR Month IN ([January], [February], [March])
) AS PivotData;
-- Returns: Each product with total quantity sold per month
```

---

## Dynamic SQL

**What it does**: Build and run SQL queries as text (useful when you don't know table/column names ahead of time).

```sql
-- Simple example: Build query as text and execute it
DECLARE @SQL NVARCHAR(MAX);
SET @SQL = 'SELECT * FROM Customers WHERE Country = ''USA''';
EXEC(@SQL);
-- Same as: SELECT * FROM Customers WHERE Country = 'USA'

-- Dynamic table name
DECLARE @TableName NVARCHAR(100) = 'Products';
DECLARE @SQL NVARCHAR(MAX);
SET @SQL = 'SELECT * FROM ' + @TableName;
EXEC(@SQL);
-- Runs: SELECT * FROM Products

-- ⚠️ DANGEROUS! Never use user input directly (SQL injection risk!)
-- BAD:
DECLARE @UserInput NVARCHAR(100) = 'USA''; DELETE FROM Customers; --';
SET @SQL = 'SELECT * FROM Customers WHERE Country = ''' + @UserInput + '''';
EXEC(@SQL);  -- DISASTER! This deletes all customers!

-- ✅ SAFE: Use sp_executesql with parameters
DECLARE @Country NVARCHAR(50) = 'USA';
DECLARE @SQL NVARCHAR(MAX);
SET @SQL = 'SELECT * FROM Customers WHERE Country = @Country';
EXEC sp_executesql @SQL, N'@Country NVARCHAR(50)', @Country;
-- Safe! @Country is treated as a value, not SQL code

-- Practical example: Dynamic search
DECLARE @SearchColumn NVARCHAR(100) = 'LastName';
DECLARE @SearchValue NVARCHAR(100) = 'Smith';
DECLARE @SQL NVARCHAR(MAX);

SET @SQL = 'SELECT * FROM Customers WHERE ' + @SearchColumn + ' = @Value';
EXEC sp_executesql @SQL, N'@Value NVARCHAR(100)', @SearchValue;
-- Searches the column specified by user

-- Build complex query
DECLARE @Columns NVARCHAR(MAX) = 'FirstName, LastName, Email';
DECLARE @Table NVARCHAR(100) = 'Customers';
DECLARE @Condition NVARCHAR(MAX) = 'Country = @Country';
DECLARE @Country NVARCHAR(50) = 'USA';

SET @SQL = 'SELECT ' + @Columns + ' FROM ' + @Table + ' WHERE ' + @Condition;
EXEC sp_executesql @SQL, N'@Country NVARCHAR(50)', @Country;

-- When to use dynamic SQL:
-- ✅ Report builders where columns change
-- ✅ Admin tools where table names vary
-- ✅ Complex search with optional filters
-- ❌ Regular queries (slower than normal SQL)
-- ❌ User input without parameters (security risk!)
```

---

## Pagination with Total Count

**What it does**: Show page 1, 2, 3... and tell user how many pages total (like Google search results).

```sql
-- Simple pagination (show 20 items per page)
DECLARE @PageNumber INT = 1;      -- Which page (1, 2, 3...)
DECLARE @PageSize INT = 20;       -- Items per page

SELECT *
FROM Products
ORDER BY ProductName
OFFSET (@PageNumber - 1) * @PageSize ROWS  -- Skip previous pages
FETCH NEXT @PageSize ROWS ONLY;             -- Get current page
-- Page 1: Rows 1-20
-- Page 2: Rows 21-40
-- Page 3: Rows 41-60

-- Pagination WITH total count (one query!)
DECLARE @PageNumber INT = 2;
DECLARE @PageSize INT = 20;

SELECT
    *,
    COUNT(*) OVER() AS TotalRecords  -- Total count without paging
FROM Products
ORDER BY ProductName
OFFSET (@PageNumber - 1) * @PageSize ROWS
FETCH NEXT @PageSize ROWS ONLY;
-- Returns: Page 2 (rows 21-40) AND total count of all products

-- Calculate total pages
DECLARE @PageNumber INT = 1;
DECLARE @PageSize INT = 20;
DECLARE @TotalRecords INT;
DECLARE @TotalPages INT;

SELECT @TotalRecords = COUNT(*) FROM Products;
SET @TotalPages = CEILING(@TotalRecords / CAST(@PageSize AS FLOAT));

SELECT
    *,
    @TotalRecords AS TotalRecords,
    @TotalPages AS TotalPages,
    @PageNumber AS CurrentPage
FROM Products
ORDER BY ProductName
OFFSET (@PageNumber - 1) * @PageSize ROWS
FETCH NEXT @PageSize ROWS ONLY;

-- Pagination with filtering
DECLARE @PageNumber INT = 1;
DECLARE @PageSize INT = 20;
DECLARE @Category NVARCHAR(50) = 'Electronics';

SELECT
    *,
    COUNT(*) OVER() AS TotalRecords
FROM Products
WHERE Category = @Category  -- Filter first
ORDER BY ProductName
OFFSET (@PageNumber - 1) * @PageSize ROWS
FETCH NEXT @PageSize ROWS ONLY;
-- Returns: Page 1 of Electronics only
```

---

## Upsert (Insert or Update)

**What it does**: If record exists, update it. If not, insert it (handles both cases automatically).

```sql
-- Method 1: IF EXISTS (simple and clear)
IF EXISTS (SELECT 1 FROM Products WHERE ProductID = 123)
BEGIN
    -- Product exists, update it
    UPDATE Products
    SET ProductName = 'Updated Name', Price = 99.99
    WHERE ProductID = 123;
END
ELSE
BEGIN
    -- Product doesn't exist, insert it
    INSERT INTO Products (ProductID, ProductName, Price)
    VALUES (123, 'Updated Name', 99.99);
END

-- Method 2: MERGE (one statement, more efficient)
MERGE INTO Products AS Target
USING (SELECT 123 AS ProductID, 'Laptop' AS ProductName, 999.99 AS Price) AS Source
ON Target.ProductID = Source.ProductID

WHEN MATCHED THEN
    UPDATE SET
        Target.ProductName = Source.ProductName,
        Target.Price = Source.Price

WHEN NOT MATCHED THEN
    INSERT (ProductID, ProductName, Price)
    VALUES (Source.ProductID, Source.ProductName, Source.Price);

-- Method 3: INSERT with WHERE NOT EXISTS (insert only if missing)
INSERT INTO Products (ProductID, ProductName, Price)
SELECT 123, 'Laptop', 999.99
WHERE NOT EXISTS (
    SELECT 1 FROM Products WHERE ProductID = 123
);
-- Inserts only if ProductID 123 doesn't exist

-- Then update separately if needed
UPDATE Products
SET ProductName = 'Laptop', Price = 999.99
WHERE ProductID = 123;

-- Practical example: Update customer or create new
DECLARE @Email NVARCHAR(100) = 'john@email.com';
DECLARE @FirstName NVARCHAR(50) = 'John';
DECLARE @LastName NVARCHAR(50) = 'Doe';
DECLARE @Phone NVARCHAR(20) = '555-1234';

MERGE INTO Customers AS Target
USING (SELECT @Email AS Email) AS Source
ON Target.Email = Source.Email

WHEN MATCHED THEN
    UPDATE SET
        Target.FirstName = @FirstName,
        Target.LastName = @LastName,
        Target.Phone = @Phone,
        Target.UpdatedDate = GETDATE()

WHEN NOT MATCHED THEN
    INSERT (Email, FirstName, LastName, Phone, CreatedDate)
    VALUES (@Email, @FirstName, @LastName, @Phone, GETDATE());
```

---

## Finding Duplicates

**What it does**: Find records that appear more than once (like duplicate emails, names, etc).

```sql
-- Simple: Find duplicate emails
SELECT Email, COUNT(*) AS DuplicateCount
FROM Customers
GROUP BY Email
HAVING COUNT(*) > 1;
-- Returns: Only emails that appear more than once

-- Show all duplicate rows with details
SELECT *
FROM Customers
WHERE Email IN (
    SELECT Email
    FROM Customers
    GROUP BY Email
    HAVING COUNT(*) > 1
);
-- Returns: All customer records with duplicate emails

-- Find duplicates across multiple columns
SELECT FirstName, LastName, COUNT(*) AS DuplicateCount
FROM Customers
GROUP BY FirstName, LastName
HAVING COUNT(*) > 1;
-- Returns: Duplicate first+last name combinations

-- Find exact duplicate rows (all columns match)
SELECT
    FirstName,
    LastName,
    Email,
    Phone,
    COUNT(*) AS DuplicateCount
FROM Customers
GROUP BY FirstName, LastName, Email, Phone
HAVING COUNT(*) > 1;

-- Show which rows are duplicates with row numbers
WITH DuplicateCTE AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY Email  -- Group by email
            ORDER BY CustomerID -- Order within each group
        ) AS RowNum
    FROM Customers
)
SELECT * FROM DuplicateCTE
WHERE RowNum > 1;
-- Returns: All duplicate rows (keeping first occurrence)

-- Find customers with same name but different email (possible duplicates)
SELECT
    c1.CustomerID AS ID1,
    c2.CustomerID AS ID2,
    c1.FirstName,
    c1.LastName,
    c1.Email AS Email1,
    c2.Email AS Email2
FROM Customers c1
INNER JOIN Customers c2
    ON c1.FirstName = c2.FirstName
    AND c1.LastName = c2.LastName
    AND c1.CustomerID < c2.CustomerID;  -- Avoid showing same pair twice
-- Returns: Customers with same name but different IDs
```

---

## Deleting Duplicates

**What it does**: Remove duplicate records, keeping only one copy.

```sql
-- Safe method: Keep first, delete rest (using ROW_NUMBER)
WITH DuplicateCTE AS (
    SELECT
        CustomerID,
        Email,
        ROW_NUMBER() OVER (
            PARTITION BY Email      -- Group duplicates
            ORDER BY CustomerID    -- Keep smallest ID
        ) AS RowNum
    FROM Customers
)
DELETE FROM DuplicateCTE
WHERE RowNum > 1;
-- Keeps: First customer with each email
-- Deletes: All other duplicates

-- Keep the newest record instead
WITH DuplicateCTE AS (
    SELECT
        CustomerID,
        Email,
        ROW_NUMBER() OVER (
            PARTITION BY Email
            ORDER BY CreatedDate DESC  -- Keep newest
        ) AS RowNum
    FROM Customers
)
DELETE FROM DuplicateCTE
WHERE RowNum > 1;
-- Keeps: Most recent customer with each email

-- Delete exact duplicates (all columns match)
WITH DuplicateCTE AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY FirstName, LastName, Email, Phone
            ORDER BY CustomerID
        ) AS RowNum
    FROM Customers
)
DELETE FROM DuplicateCTE
WHERE RowNum > 1;

-- ⚠️ Always test first! Use SELECT before DELETE
WITH DuplicateCTE AS (
    SELECT
        *,
        ROW_NUMBER() OVER (
            PARTITION BY Email
            ORDER BY CustomerID
        ) AS RowNum
    FROM Customers
)
SELECT * FROM DuplicateCTE WHERE RowNum > 1;
-- Check results, then change SELECT to DELETE

-- Alternative: Keep record with most data
WITH DuplicateCTE AS (
    SELECT
        CustomerID,
        Email,
        ROW_NUMBER() OVER (
            PARTITION BY Email
            ORDER BY
                CASE WHEN Phone IS NULL THEN 1 ELSE 0 END,  -- Prefer records with phone
                CASE WHEN Address IS NULL THEN 1 ELSE 0 END, -- Prefer records with address
                CustomerID  -- Tie-breaker
        ) AS RowNum
    FROM Customers
)
DELETE FROM DuplicateCTE
WHERE RowNum > 1;
-- Keeps: Record with most complete data
```

---

## Running Totals & Cumulative Sums

**What it does**: Show cumulative total as you go through rows (like bank balance after each transaction).

```sql
-- Simple running total
SELECT
    OrderDate,
    TotalAmount,
    SUM(TotalAmount) OVER (ORDER BY OrderDate) AS RunningTotal
FROM Orders
ORDER BY OrderDate;
-- Returns:
-- 2024-01-01 | 100  | 100   (total so far)
-- 2024-01-02 | 200  | 300   (100 + 200)
-- 2024-01-03 | 150  | 450   (100 + 200 + 150)

-- Running total by customer
SELECT
    CustomerID,
    OrderDate,
    TotalAmount,
    SUM(TotalAmount) OVER (
        PARTITION BY CustomerID  -- Separate total for each customer
        ORDER BY OrderDate
    ) AS CustomerRunningTotal
FROM Orders
ORDER BY CustomerID, OrderDate;
-- Returns: Cumulative spending for each customer separately

-- Running average
SELECT
    OrderDate,
    TotalAmount,
    AVG(TotalAmount) OVER (
        ORDER BY OrderDate
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS RunningAverage
FROM Orders
ORDER BY OrderDate;
-- Returns: Average of all orders up to this date

-- Running count
SELECT
    OrderDate,
    OrderID,
    ROW_NUMBER() OVER (ORDER BY OrderDate) AS OrderNumber,
    COUNT(*) OVER (ORDER BY OrderDate) AS TotalOrdersSoFar
FROM Orders
ORDER BY OrderDate;
-- Returns: Each order with cumulative count

-- Moving average (last 7 days)
SELECT
    OrderDate,
    TotalAmount,
    AVG(TotalAmount) OVER (
        ORDER BY OrderDate
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW  -- Last 7 rows (6 + current)
    ) AS MovingAverage7Days
FROM Orders
ORDER BY OrderDate;
-- Returns: Average of current day plus previous 6 days

-- Year-to-date total
SELECT
    OrderDate,
    TotalAmount,
    SUM(TotalAmount) OVER (
        PARTITION BY YEAR(OrderDate)  -- Reset each year
        ORDER BY OrderDate
    ) AS YearToDateTotal
FROM Orders
ORDER BY OrderDate;
-- Returns: Cumulative total that resets every January 1st

-- Practical example: Bank account balance
SELECT
    TransactionDate,
    TransactionType,
    Amount,
    SUM(CASE
        WHEN TransactionType = 'Deposit' THEN Amount
        WHEN TransactionType = 'Withdrawal' THEN -Amount
    END) OVER (ORDER BY TransactionDate) AS Balance
FROM Transactions
ORDER BY TransactionDate;
-- Returns: Running balance after each deposit/withdrawal
```

---

## SQL Injection Prevention

**What it does**: Protect your database from hackers who try to run malicious SQL through your application.

```sql
-- ⚠️ DANGEROUS! Never build SQL with user input directly
DECLARE @UserInput NVARCHAR(100) = 'Smith';
DECLARE @SQL NVARCHAR(MAX);
SET @SQL = 'SELECT * FROM Customers WHERE LastName = ''' + @UserInput + '''';
EXEC(@SQL);
-- If user enters: Smith' OR '1'='1
-- Query becomes: SELECT * FROM Customers WHERE LastName = 'Smith' OR '1'='1'
-- Returns ALL customers! (security breach)

-- ✅ SAFE: Use parameterized queries
DECLARE @LastName NVARCHAR(100) = @UserInput;
SELECT * FROM Customers WHERE LastName = @LastName;
-- Safe! @LastName is treated as data, not SQL code

-- ✅ SAFE: In stored procedures, use parameters
CREATE PROCEDURE GetCustomerByName
    @LastName NVARCHAR(100)  -- Parameter, not concatenated string
AS
BEGIN
    SELECT * FROM Customers WHERE LastName = @LastName;
END;
-- Always safe because @LastName can't contain SQL commands

EXEC GetCustomerByName @LastName = 'Smith';

-- ✅ SAFE: With dynamic SQL, use sp_executesql
DECLARE @SQL NVARCHAR(MAX);
DECLARE @LastName NVARCHAR(100) = @UserInput;

SET @SQL = 'SELECT * FROM Customers WHERE LastName = @LastName';
EXEC sp_executesql @SQL, N'@LastName NVARCHAR(100)', @LastName;
-- Safe! @LastName parameter is properly escaped

-- ✅ SAFE: Validate user input first
DECLARE @UserInput NVARCHAR(100) = 'Smith';

-- Check for dangerous characters
IF @UserInput LIKE '%[;''"--]%'
BEGIN
    RAISERROR('Invalid input detected', 16, 1);
    RETURN;
END

SELECT * FROM Customers WHERE LastName = @UserInput;

-- ✅ SAFE: Whitelist allowed values
DECLARE @SortColumn NVARCHAR(50) = @UserInput;

-- Only allow specific column names
IF @SortColumn NOT IN ('FirstName', 'LastName', 'Email', 'CreatedDate')
BEGIN
    SET @SortColumn = 'CustomerID';  -- Default safe value
END

DECLARE @SQL NVARCHAR(MAX);
SET @SQL = 'SELECT * FROM Customers ORDER BY ' + @SortColumn;
EXEC(@SQL);
-- Safe because we validated @SortColumn

-- ❌ Common vulnerable patterns:
-- Login check (NEVER do this!)
DECLARE @Username NVARCHAR(100) = @UserInput;
DECLARE @Password NVARCHAR(100) = @PasswordInput;
SET @SQL = 'SELECT * FROM Users WHERE Username = ''' + @Username + ''' AND Password = ''' + @Password + '''';
-- If user enters: admin' --
-- Query becomes: SELECT * FROM Users WHERE Username = 'admin' --' AND Password = '...'
-- Logs in as admin without password!

-- ✅ CORRECT login check:
SELECT * FROM Users
WHERE Username = @Username AND PasswordHash = HASHBYTES('SHA2_256', @Password);
-- Parameters are safe, and passwords are hashed

-- Summary of protection methods:
-- 1. Use parameters (not string concatenation)
-- 2. Use stored procedures with parameters
-- 3. Validate/sanitize all user input
-- 4. Use whitelists for dynamic values (columns, tables)
-- 5. Never trust user input
-- 6. Use sp_executesql for dynamic SQL
```

---

## Performance Best Practices

**What it does**: Make your queries run faster and use less server resources.

```sql
-- 1. Use indexes on WHERE and JOIN columns
CREATE INDEX IX_Customers_Email ON Customers(Email);
-- Makes: WHERE Email = 'x' and JOIN ON Email much faster

-- 2. Avoid SELECT *, specify columns
-- ❌ Slow:
SELECT * FROM Customers;

-- ✅ Fast:
SELECT CustomerID, FirstName, Email FROM Customers;
-- Only gets what you need, less data transfer

-- 3. Use EXISTS instead of IN for subqueries
-- ❌ Slower:
SELECT * FROM Customers
WHERE CustomerID IN (SELECT CustomerID FROM Orders);

-- ✅ Faster:
SELECT * FROM Customers c
WHERE EXISTS (SELECT 1 FROM Orders o WHERE o.CustomerID = c.CustomerID);
-- Stops searching once found

-- 4. Use NOCOUNT in procedures (reduces network traffic)
CREATE PROCEDURE GetCustomers
AS
BEGIN
    SET NOCOUNT ON;  -- Don't send "X rows affected" message
    SELECT * FROM Customers;
END;

-- 5. Avoid functions on columns in WHERE (prevents index use)
-- ❌ Slow (can't use index):
SELECT * FROM Orders WHERE YEAR(OrderDate) = 2024;

-- ✅ Fast (can use index):
SELECT * FROM Orders
WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01';

-- 6. Use UNION ALL instead of UNION (when duplicates ok)
-- ❌ Slower:
SELECT City FROM Customers
UNION  -- Removes duplicates (extra work)
SELECT City FROM Suppliers;

-- ✅ Faster:
SELECT City FROM Customers
UNION ALL  -- Keeps duplicates (no extra work)
SELECT City FROM Suppliers;

-- 7. Limit JOIN results early with WHERE
-- ❌ Slow:
SELECT * FROM Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID
WHERE o.OrderDate >= '2024-01-01';

-- ✅ Better (filter before joining if possible):
SELECT * FROM
(SELECT * FROM Orders WHERE OrderDate >= '2024-01-01') o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID;

-- 8. Use covering indexes for frequently run queries
CREATE INDEX IX_Orders_CustomerDate
ON Orders(CustomerID, OrderDate)
INCLUDE (TotalAmount);  -- Include columns you SELECT
-- Query can get all data from index without accessing table

-- 9. Update statistics for accurate query plans
UPDATE STATISTICS Customers;
-- Helps SQL Server choose best query execution plan

-- 10. Avoid cursors, use set-based operations
-- ❌ Very slow (processes row by row):
DECLARE @CustomerID INT;
DECLARE customer_cursor CURSOR FOR SELECT CustomerID FROM Customers;
OPEN customer_cursor;
FETCH NEXT FROM customer_cursor INTO @CustomerID;
WHILE @@FETCH_STATUS = 0
BEGIN
    UPDATE Orders SET Status = 'Processed' WHERE CustomerID = @CustomerID;
    FETCH NEXT FROM customer_cursor INTO @CustomerID;
END;
CLOSE customer_cursor;
DEALLOCATE customer_cursor;

-- ✅ Fast (processes all at once):
UPDATE Orders SET Status = 'Processed'
WHERE CustomerID IN (SELECT CustomerID FROM Customers);

-- 11. Use temp tables for complex queries
-- ❌ Slow (subquery runs multiple times):
SELECT * FROM (complex subquery) WHERE ...
INNER JOIN (same complex subquery) ON ...

-- ✅ Fast (runs once, stores result):
SELECT * INTO #TempResults FROM (complex subquery);
CREATE INDEX IX_Temp ON #TempResults(ID);
SELECT * FROM #TempResults WHERE ...

-- 12. Batch large operations
-- ❌ Slow (locks table for long time):
DELETE FROM Orders WHERE OrderDate < '2020-01-01';  -- 1 million rows

-- ✅ Better (delete in chunks):
WHILE 1=1
BEGIN
    DELETE TOP (1000) FROM Orders WHERE OrderDate < '2020-01-01';
    IF @@ROWCOUNT < 1000 BREAK;
    WAITFOR DELAY '00:00:01';  -- Give other queries a chance
END;
```

---

## Troubleshooting Slow Queries

**What it does**: Find and fix queries that run slowly.

```sql
-- 1. Find currently running queries
SELECT
    session_id,
    status,
    command,
    cpu_time,
    total_elapsed_time / 1000 AS elapsed_seconds,
    wait_type,
    blocking_session_id,
    text
FROM sys.dm_exec_requests
CROSS APPLY sys.dm_exec_sql_text(sql_handle)
WHERE session_id > 50  -- Exclude system processes
ORDER BY cpu_time DESC;
-- Shows: What's running right now, how long, what's waiting

-- 2. Find slow queries from cache
SELECT TOP 20
    total_elapsed_time / execution_count / 1000000 AS avg_seconds,
    execution_count,
    total_worker_time / 1000000 AS total_cpu_seconds,
    SUBSTRING(text, (statement_start_offset/2)+1,
        ((CASE statement_end_offset
            WHEN -1 THEN DATALENGTH(text)
            ELSE statement_end_offset
        END - statement_start_offset)/2) + 1) AS query_text
FROM sys.dm_exec_query_stats
CROSS APPLY sys.dm_exec_sql_text(sql_handle)
ORDER BY avg_seconds DESC;
-- Shows: Slowest queries on average

-- 3. See execution plan (shows how SQL Server runs your query)
SET SHOWPLAN_TEXT ON;
GO
SELECT * FROM Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID
WHERE o.OrderDate >= '2024-01-01';
GO
SET SHOWPLAN_TEXT OFF;
-- Shows: Steps SQL Server takes (scans, seeks, joins)

-- 4. Check for missing indexes
SELECT
    user_seeks * avg_total_user_cost * (avg_user_impact * 0.01) AS improvement_measure,
    'CREATE INDEX IX_' + object_name(object_id) + '_' +
        CAST(migs.index_group_handle AS VARCHAR) +
        ' ON ' + mid.statement + ' (' + ISNULL(mid.equality_columns, '') +
        CASE WHEN mid.inequality_columns IS NOT NULL
            THEN CASE WHEN mid.equality_columns IS NOT NULL THEN ',' ELSE '' END +
                mid.inequality_columns ELSE '' END + ')' +
        CASE WHEN mid.included_columns IS NOT NULL
            THEN ' INCLUDE (' + mid.included_columns + ')' ELSE '' END AS create_index_statement
FROM sys.dm_db_missing_index_groups mig
INNER JOIN sys.dm_db_missing_index_group_stats migs ON mig.index_group_handle = migs.group_handle
INNER JOIN sys.dm_db_missing_index_details mid ON mig.index_handle = mid.index_handle
ORDER BY improvement_measure DESC;
-- Shows: Indexes that would help performance

-- 5. Find blocking queries
SELECT
    blocking.session_id AS blocking_session,
    blocked.session_id AS blocked_session,
    blocking_text.text AS blocking_query,
    blocked_text.text AS blocked_query,
    blocked.wait_type,
    blocked.wait_time / 1000 AS wait_seconds
FROM sys.dm_exec_requests blocked
INNER JOIN sys.dm_exec_requests blocking ON blocked.blocking_session_id = blocking.session_id
CROSS APPLY sys.dm_exec_sql_text(blocking.sql_handle) blocking_text
CROSS APPLY sys.dm_exec_sql_text(blocked.sql_handle) blocked_text;
-- Shows: Which query is blocking which

-- 6. Kill a stuck query (use carefully!)
-- First find the session_id from query above
KILL 53;  -- Replace 53 with actual session_id
-- Terminates the query

-- 7. Check table statistics (outdated stats = slow queries)
EXEC sp_helpstats 'Customers';
-- Shows: When stats were last updated

UPDATE STATISTICS Customers;  -- Update if old

-- 8. Find unused indexes (waste space and slow down INSERT/UPDATE)
SELECT
    OBJECT_NAME(i.object_id) AS table_name,
    i.name AS index_name,
    user_seeks,
    user_scans,
    user_lookups,
    user_updates
FROM sys.dm_db_index_usage_stats s
RIGHT JOIN sys.indexes i ON s.object_id = i.object_id AND s.index_id = i.index_id
WHERE OBJECTPROPERTY(i.object_id, 'IsUserTable') = 1
  AND s.database_id = DB_ID()
  AND user_seeks = 0
  AND user_scans = 0
  AND user_lookups = 0
ORDER BY user_updates DESC;
-- Shows: Indexes never used for reads (consider dropping)

-- 9. Simple query optimization tips:
-- ❌ Problem: Table scan (reads entire table)
SELECT * FROM Customers WHERE Email = 'john@email.com';

-- ✅ Solution: Add index
CREATE INDEX IX_Customers_Email ON Customers(Email);

-- ❌ Problem: Function on column prevents index use
SELECT * FROM Orders WHERE YEAR(OrderDate) = 2024;

-- ✅ Solution: Avoid function on column
SELECT * FROM Orders WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01';
```

---

## Inventory Management

**What it does**: Common queries for tracking products, stock levels, and inventory changes.

```sql
-- Current stock levels (simple)
SELECT
    ProductID,
    ProductName,
    StockQuantity,
    CASE
        WHEN StockQuantity = 0 THEN 'Out of Stock'
        WHEN StockQuantity < 10 THEN 'Low Stock'
        ELSE 'In Stock'
    END AS StockStatus
FROM Products
ORDER BY StockQuantity ASC;

-- Products that need reordering
SELECT
    ProductID,
    ProductName,
    StockQuantity,
    ReorderLevel,
    ReorderLevel - StockQuantity AS QuantityToOrder
FROM Products
WHERE StockQuantity < ReorderLevel
ORDER BY StockQuantity ASC;
-- Shows: Products below reorder point and how many to order

-- Inventory value (total worth of stock)
SELECT
    Category,
    SUM(StockQuantity * UnitCost) AS InventoryValue,
    SUM(StockQuantity) AS TotalUnits
FROM Products
GROUP BY Category
ORDER BY InventoryValue DESC;
-- Shows: Total value of inventory by category

-- Product movement (track stock changes)
SELECT
    p.ProductName,
    im.MovementDate,
    im.MovementType,  -- 'Purchase', 'Sale', 'Return', 'Adjustment'
    im.Quantity,
    im.QuantityBefore,
    im.QuantityAfter
FROM InventoryMovements im
INNER JOIN Products p ON im.ProductID = p.ProductID
WHERE im.ProductID = 123
ORDER BY im.MovementDate DESC;
-- Shows: Complete history of stock changes for a product

-- Fast-moving vs slow-moving products
SELECT
    p.ProductID,
    p.ProductName,
    COUNT(od.OrderID) AS TimesSold,
    SUM(od.Quantity) AS TotalQuantitySold,
    CASE
        WHEN COUNT(od.OrderID) > 50 THEN 'Fast-moving'
        WHEN COUNT(od.OrderID) > 10 THEN 'Medium'
        ELSE 'Slow-moving'
    END AS MovementCategory
FROM Products p
LEFT JOIN OrderDetails od ON p.ProductID = od.ProductID
WHERE od.OrderDate >= DATEADD(MONTH, -3, GETDATE())  -- Last 3 months
GROUP BY p.ProductID, p.ProductName
ORDER BY TotalQuantitySold DESC;

-- Stock turnover rate (how fast inventory sells)
SELECT
    ProductID,
    ProductName,
    (TotalSold * 1.0 / NULLIF(AverageStock, 0)) AS TurnoverRate
FROM (
    SELECT
        p.ProductID,
        p.ProductName,
        SUM(od.Quantity) AS TotalSold,
        AVG(p.StockQuantity) AS AverageStock
    FROM Products p
    LEFT JOIN OrderDetails od ON p.ProductID = od.ProductID
    WHERE od.OrderDate >= DATEADD(YEAR, -1, GETDATE())
    GROUP BY p.ProductID, p.ProductName
) AS StockData
ORDER BY TurnoverRate DESC;
-- Higher = sells faster
```

---

## Sales Reports

**What it does**: Common sales analysis queries for business reporting.

```sql
-- Daily sales summary
SELECT
    CAST(OrderDate AS DATE) AS SaleDate,
    COUNT(OrderID) AS NumberOfOrders,
    SUM(TotalAmount) AS TotalSales,
    AVG(TotalAmount) AS AverageOrderValue,
    MIN(TotalAmount) AS SmallestOrder,
    MAX(TotalAmount) AS LargestOrder
FROM Orders
WHERE OrderDate >= DATEADD(MONTH, -1, GETDATE())  -- Last month
GROUP BY CAST(OrderDate AS DATE)
ORDER BY SaleDate DESC;

-- Monthly sales comparison (this year vs last year)
SELECT
    MONTH(OrderDate) AS Month,
    SUM(CASE WHEN YEAR(OrderDate) = 2024 THEN TotalAmount ELSE 0 END) AS Sales2024,
    SUM(CASE WHEN YEAR(OrderDate) = 2023 THEN TotalAmount ELSE 0 END) AS Sales2023,
    SUM(CASE WHEN YEAR(OrderDate) = 2024 THEN TotalAmount ELSE 0 END) -
    SUM(CASE WHEN YEAR(OrderDate) = 2023 THEN TotalAmount ELSE 0 END) AS Difference
FROM Orders
WHERE YEAR(OrderDate) IN (2023, 2024)
GROUP BY MONTH(OrderDate)
ORDER BY Month;

-- Top 10 customers by revenue
SELECT TOP 10
    c.CustomerID,
    c.FirstName + ' ' + c.LastName AS CustomerName,
    COUNT(o.OrderID) AS TotalOrders,
    SUM(o.TotalAmount) AS TotalSpent,
    AVG(o.TotalAmount) AS AverageOrderValue,
    MAX(o.OrderDate) AS LastOrderDate
FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.FirstName, c.LastName
ORDER BY TotalSpent DESC;

-- Sales by product category
SELECT
    p.Category,
    COUNT(DISTINCT o.OrderID) AS NumberOfOrders,
    SUM(od.Quantity) AS UnitsSold,
    SUM(od.Quantity * od.UnitPrice) AS TotalRevenue,
    AVG(od.UnitPrice) AS AveragePrice
FROM Products p
INNER JOIN OrderDetails od ON p.ProductID = od.ProductID
INNER JOIN Orders o ON od.OrderID = o.OrderID
WHERE o.OrderDate >= '2024-01-01'
GROUP BY p.Category
ORDER BY TotalRevenue DESC;

-- Sales trend (running total by month)
SELECT
    YEAR(OrderDate) AS Year,
    MONTH(OrderDate) AS Month,
    SUM(TotalAmount) AS MonthlySales,
    SUM(SUM(TotalAmount)) OVER (
        PARTITION BY YEAR(OrderDate)
        ORDER BY MONTH(OrderDate)
    ) AS YearToDateSales
FROM Orders
GROUP BY YEAR(OrderDate), MONTH(OrderDate)
ORDER BY Year, Month;

-- Customer retention (repeat customers)
SELECT
    YEAR(o.OrderDate) AS Year,
    COUNT(DISTINCT o.CustomerID) AS TotalCustomers,
    COUNT(DISTINCT CASE WHEN o2.OrderID IS NOT NULL THEN o.CustomerID END) AS ReturnedCustomers,
    CAST(COUNT(DISTINCT CASE WHEN o2.OrderID IS NOT NULL THEN o.CustomerID END) * 100.0 /
        COUNT(DISTINCT o.CustomerID) AS DECIMAL(5,2)) AS RetentionRate
FROM Orders o
LEFT JOIN Orders o2
    ON o.CustomerID = o2.CustomerID
    AND o2.OrderDate > o.OrderDate
    AND DATEDIFF(DAY, o.OrderDate, o2.OrderDate) <= 90  -- Returned within 90 days
GROUP BY YEAR(o.OrderDate)
ORDER BY Year;

-- Best selling products
SELECT TOP 20
    p.ProductID,
    p.ProductName,
    p.Category,
    SUM(od.Quantity) AS TotalSold,
    SUM(od.Quantity * od.UnitPrice) AS Revenue,
    COUNT(DISTINCT od.OrderID) AS NumberOfOrders
FROM Products p
INNER JOIN OrderDetails od ON p.ProductID = od.ProductID
INNER JOIN Orders o ON od.OrderID = o.OrderID
WHERE o.OrderDate >= DATEADD(MONTH, -3, GETDATE())  -- Last 3 months
GROUP BY p.ProductID, p.ProductName, p.Category
ORDER BY TotalSold DESC;
```

---

## User Activity Tracking

**What it does**: Track what users do in your application (logins, actions, page views).

```sql
-- Create activity log table
CREATE TABLE UserActivityLog (
    LogID INT IDENTITY(1,1) PRIMARY KEY,
    UserID INT NOT NULL,
    ActivityType NVARCHAR(50) NOT NULL,  -- 'Login', 'PageView', 'Purchase', etc.
    ActivityDetails NVARCHAR(MAX),
    IPAddress NVARCHAR(50),
    ActivityDate DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (UserID) REFERENCES Users(UserID)
);

-- Log a user action
INSERT INTO UserActivityLog (UserID, ActivityType, ActivityDetails, IPAddress)
VALUES (123, 'Login', 'Successful login', '192.168.1.1');

-- Daily active users
SELECT
    CAST(ActivityDate AS DATE) AS Date,
    COUNT(DISTINCT UserID) AS ActiveUsers
FROM UserActivityLog
WHERE ActivityDate >= DATEADD(DAY, -30, GETDATE())
GROUP BY CAST(ActivityDate AS DATE)
ORDER BY Date;

-- User session duration
SELECT
    UserID,
    MIN(ActivityDate) AS SessionStart,
    MAX(ActivityDate) AS SessionEnd,
    DATEDIFF(MINUTE, MIN(ActivityDate), MAX(ActivityDate)) AS SessionMinutes
FROM UserActivityLog
WHERE CAST(ActivityDate AS DATE) = '2024-11-18'
GROUP BY UserID, CAST(ActivityDate AS DATE)
ORDER BY SessionMinutes DESC;

-- Most viewed pages
SELECT
    ActivityDetails AS PageURL,
    COUNT(*) AS ViewCount,
    COUNT(DISTINCT UserID) AS UniqueUsers
FROM UserActivityLog
WHERE ActivityType = 'PageView'
  AND ActivityDate >= DATEADD(DAY, -7, GETDATE())
GROUP BY ActivityDetails
ORDER BY ViewCount DESC;

-- User retention by cohort (users who signed up same month)
SELECT
    FORMAT(u.SignupDate, 'yyyy-MM') AS SignupMonth,
    COUNT(DISTINCT u.UserID) AS NewUsers,
    COUNT(DISTINCT CASE WHEN a.ActivityDate >= DATEADD(MONTH, 1, u.SignupDate) THEN a.UserID END) AS ActiveMonth1,
    COUNT(DISTINCT CASE WHEN a.ActivityDate >= DATEADD(MONTH, 2, u.SignupDate) THEN a.UserID END) AS ActiveMonth2,
    COUNT(DISTINCT CASE WHEN a.ActivityDate >= DATEADD(MONTH, 3, u.SignupDate) THEN a.UserID END) AS ActiveMonth3
FROM Users u
LEFT JOIN UserActivityLog a ON u.UserID = a.UserID
GROUP BY FORMAT(u.SignupDate, 'yyyy-MM')
ORDER BY SignupMonth;

-- Find inactive users (haven't logged in for 30 days)
SELECT
    u.UserID,
    u.Username,
    u.Email,
    MAX(a.ActivityDate) AS LastActivity,
    DATEDIFF(DAY, MAX(a.ActivityDate), GETDATE()) AS DaysSinceActive
FROM Users u
LEFT JOIN UserActivityLog a ON u.UserID = a.UserID
GROUP BY u.UserID, u.Username, u.Email
HAVING MAX(a.ActivityDate) < DATEADD(DAY, -30, GETDATE())
    OR MAX(a.ActivityDate) IS NULL
ORDER BY DaysSinceActive DESC;

-- User engagement score
SELECT
    UserID,
    COUNT(*) AS TotalActions,
    COUNT(DISTINCT CAST(ActivityDate AS DATE)) AS ActiveDays,
    COUNT(DISTINCT ActivityType) AS DifferentActions,
    -- Simple engagement score
    (COUNT(*) * 0.5) +  -- 0.5 points per action
    (COUNT(DISTINCT CAST(ActivityDate AS DATE)) * 2) +  -- 2 points per active day
    (COUNT(DISTINCT ActivityType) * 5) AS EngagementScore  -- 5 points per action type
FROM UserActivityLog
WHERE ActivityDate >= DATEADD(DAY, -30, GETDATE())
GROUP BY UserID
ORDER BY EngagementScore DESC;
```

---

## Type Conversion & Casting

**What it does**: Change data from one type to another (text to number, date to text, etc).

```sql
-- CAST - convert between types
SELECT CAST('123' AS INT) AS NumberFromText;          -- '123' → 123
SELECT CAST(123 AS NVARCHAR(10)) AS TextFromNumber;   -- 123 → '123'
SELECT CAST('2024-11-18' AS DATE) AS DateFromText;    -- '2024-11-18' → 2024-11-18
SELECT CAST(123.456 AS INT) AS RoundDown;             -- 123.456 → 123 (truncates)

-- CONVERT - same as CAST but with format options
SELECT CONVERT(INT, '123') AS NumberFromText;
SELECT CONVERT(NVARCHAR(10), GETDATE(), 101) AS USDate;  -- 11/18/2024
SELECT CONVERT(NVARCHAR(10), GETDATE(), 103) AS UKDate;  -- 18/11/2024

-- TRY_CAST and TRY_CONVERT (safe versions - return NULL if fails)
SELECT TRY_CAST('abc' AS INT) AS Result;              -- Returns NULL (not an error)
SELECT CAST('abc' AS INT) AS Result;                  -- ERROR!

SELECT TRY_CONVERT(INT, 'not a number') AS SafeConvert;  -- Returns NULL

-- String to number
SELECT CAST('123' AS INT) AS IntValue;
SELECT CAST('123.45' AS DECIMAL(10,2)) AS DecimalValue;
SELECT CAST('123.45' AS FLOAT) AS FloatValue;

-- Number to string
SELECT CAST(123 AS NVARCHAR(10)) AS TextValue;
SELECT STR(123.456, 10, 2) AS FormattedText;  -- '    123.46' (10 chars, 2 decimals)

-- String to date
SELECT CAST('2024-11-18' AS DATE) AS DateValue;
SELECT CAST('2024-11-18 14:30:00' AS DATETIME) AS DateTimeValue;
SELECT CONVERT(DATE, '11/18/2024', 101) AS USFormat;  -- MM/DD/YYYY
SELECT CONVERT(DATE, '18/11/2024', 103) AS UKFormat;  -- DD/MM/YYYY

-- Date to string
SELECT CAST(GETDATE() AS NVARCHAR(20)) AS DefaultFormat;
SELECT CONVERT(NVARCHAR(10), GETDATE(), 101) AS USDate;     -- MM/DD/YYYY
SELECT CONVERT(NVARCHAR(10), GETDATE(), 103) AS UKDate;     -- DD/MM/YYYY
SELECT CONVERT(NVARCHAR(20), GETDATE(), 120) AS ISOFormat;  -- YYYY-MM-DD HH:MM:SS
SELECT FORMAT(GETDATE(), 'yyyy-MM-dd') AS CustomFormat;     -- 2024-11-18

-- Implicit conversion (automatic, but can be slow)
SELECT * FROM Products WHERE ProductID = '123';  -- '123' auto-converts to 123
-- Better: Use correct type
SELECT * FROM Products WHERE ProductID = 123;

-- Common conversions cheat sheet:
/*
Text to Number:
  CAST('123' AS INT)
  CAST('123.45' AS DECIMAL(10,2))
  TRY_CAST('abc' AS INT)  -- Safe version

Number to Text:
  CAST(123 AS VARCHAR(10))
  STR(123.456, 10, 2)

Text to Date:
  CAST('2024-11-18' AS DATE)
  CONVERT(DATE, '11/18/2024', 101)

Date to Text:
  CONVERT(VARCHAR(10), GETDATE(), 101)  -- MM/DD/YYYY
  FORMAT(GETDATE(), 'yyyy-MM-dd')       -- Custom format

NULL handling:
  ISNULL(Phone, 'N/A')
  COALESCE(Phone, Mobile, 'N/A')
*/
```

---

## Common Mistakes & Gotchas

**What to watch out for**: Common errors and how to avoid them.

```sql
-- ❌ MISTAKE 1: Forgetting WHERE in UPDATE/DELETE
UPDATE Products SET Price = 0;  -- DANGER: Updates ALL products!
DELETE FROM Orders;              -- DANGER: Deletes ALL orders!

-- ✅ SOLUTION: Always use WHERE (or use transaction)
BEGIN TRANSACTION;
UPDATE Products SET Price = 0 WHERE Category = 'Clearance';
-- Check results first
SELECT * FROM Products WHERE Category = 'Clearance';
-- If looks good:
COMMIT;
-- If wrong:
ROLLBACK;

-- ❌ MISTAKE 2: Using = NULL instead of IS NULL
SELECT * FROM Customers WHERE Phone = NULL;   -- Returns nothing!
-- ✅ CORRECT:
SELECT * FROM Customers WHERE Phone IS NULL;

-- ❌ MISTAKE 3: Forgetting columns in GROUP BY
SELECT Category, ProductName, COUNT(*)  -- ERROR!
FROM Products
GROUP BY Category;
-- ✅ CORRECT: Include all non-aggregate columns
SELECT Category, ProductName, COUNT(*)
FROM Products
GROUP BY Category, ProductName;

-- ❌ MISTAKE 4: String concatenation with NULL
SELECT FirstName + ' ' + LastName AS FullName FROM Customers;
-- Returns: NULL if either name is NULL
-- ✅ SOLUTION: Use CONCAT or ISNULL
SELECT CONCAT(FirstName, ' ', LastName) AS FullName FROM Customers;
SELECT ISNULL(FirstName, '') + ' ' + ISNULL(LastName, '') AS FullName FROM Customers;

-- ❌ MISTAKE 5: Using SELECT * in production
SELECT * FROM LargeTable;  -- Slow, wastes bandwidth
-- ✅ SOLUTION: Select only needed columns
SELECT CustomerID, FirstName, Email FROM Customers;

-- ❌ MISTAKE 6: Not using transactions for related operations
UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;
-- Power outage here = money lost!
UPDATE Accounts SET Balance = Balance + 100 WHERE AccountID = 2;

-- ✅ CORRECT: Use transaction
BEGIN TRANSACTION;
    UPDATE Accounts SET Balance = Balance - 100 WHERE AccountID = 1;
    UPDATE Accounts SET Balance = Balance + 100 WHERE AccountID = 2;
COMMIT;

-- ❌ MISTAKE 7: Comparing different data types
SELECT * FROM Products WHERE ProductID = '5';  -- Works but slow
-- ✅ CORRECT: Use correct type
SELECT * FROM Products WHERE ProductID = 5;

-- ❌ MISTAKE 8: Using DISTINCT to hide problems
SELECT DISTINCT CustomerID, OrderID FROM Orders;
-- Why are there duplicates? Fix the root cause!
-- ✅ BETTER: Find and fix why duplicates exist

-- ❌ MISTAKE 9: Not handling division by zero
SELECT TotalAmount / OrderCount FROM CustomerStats;  -- Error if OrderCount = 0
-- ✅ SOLUTION:
SELECT TotalAmount / NULLIF(OrderCount, 0) FROM CustomerStats;
-- Or:
SELECT
    CASE
        WHEN OrderCount = 0 THEN 0
        ELSE TotalAmount / OrderCount
    END AS AvgOrder
FROM CustomerStats;

-- ❌ MISTAKE 10: Over-using cursors (very slow)
-- Cursors process row by row (slow)
-- ✅ SOLUTION: Use set-based operations instead

-- ⚠️ GOTCHA: COUNT(*) vs COUNT(column)
SELECT
    COUNT(*) AS AllRows,        -- Counts all rows
    COUNT(Phone) AS WithPhone   -- Counts non-NULL only
FROM Customers;

-- ⚠️ GOTCHA: ORDER BY required for consistent ordering
SELECT TOP 10 * FROM Customers;  -- Random 10 customers!
-- ✅ CORRECT:
SELECT TOP 10 * FROM Customers ORDER BY CustomerID;

-- ⚠️ GOTCHA: Date comparisons with time
SELECT * FROM Orders WHERE OrderDate = '2024-01-15';
-- Might miss orders if OrderDate includes time!
-- ✅ BETTER:
SELECT * FROM Orders WHERE CAST(OrderDate AS DATE) = '2024-01-15';
-- Or:
SELECT * FROM Orders
WHERE OrderDate >= '2024-01-15' AND OrderDate < '2024-01-16';
```

---

## Quick Tips

### Performance Tips
- Always use WHERE clauses with UPDATE/DELETE
- Create indexes on frequently queried columns
- Avoid SELECT *, specify columns
- Use EXISTS instead of COUNT(*) when checking if records exist
- Use UNION ALL instead of UNION when duplicates don't matter (faster)

### Data Safety Tips
- Always test SELECT before running UPDATE/DELETE
- Use transactions for related operations
- Back up before major changes
- Test with small datasets first

### Code Quality Tips
- Use meaningful aliases (c for Customers, not t1)
- Format SQL for readability
- Comment complex queries
- Use consistent naming conventions

---

**Remember**: When in doubt, test your query with SELECT first! 🚀
