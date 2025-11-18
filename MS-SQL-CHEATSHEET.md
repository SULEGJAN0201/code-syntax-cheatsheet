# MS SQL Syntax Cheat Sheet
## Microsoft SQL Server - Quick Reference Guide

> **Quick Guide**: This cheat sheet helps you quickly remember MS SQL syntax for common database operations. Each example includes comments showing the expected results!

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

### ⚠️ Common Pitfalls
37. [Common Mistakes & Gotchas](#common-mistakes--gotchas)

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
