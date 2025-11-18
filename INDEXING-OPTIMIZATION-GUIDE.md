# Database Indexing & Query Optimization Guide
## Complete Reference for Performance Tuning

> **For Senior Engineers**: A comprehensive guide covering indexing fundamentals, advanced optimization techniques, query tuning, and performance best practices for production databases.

---

## 📋 Table of Contents

1. [Quick Reference](#-quick-reference-card)
2. [Understanding Indexes](#-understanding-indexes)
3. [Index Types](#-index-types-detailed)
4. [Creating Indexes](#-creating-indexes)
5. [Query Optimization](#-query-optimization)
6. [Execution Plans](#-reading-execution-plans)
7. [Index Maintenance](#-index-maintenance)
8. [Advanced Optimization](#-advanced-optimization-techniques)
9. [Performance Monitoring](#-performance-monitoring)
10. [Best Practices](#-best-practices--common-pitfalls)

---

## ⚡ Quick Reference Card

**Most Common Index Operations**

```sql
-- Create a basic index
CREATE INDEX idx_lastname ON Customers(LastName);

-- Create a composite index (multiple columns)
CREATE INDEX idx_name_email ON Customers(LastName, FirstName, Email);

-- Create a unique index
CREATE UNIQUE INDEX idx_email ON Customers(Email);

-- Create a covering index (includes all needed columns)
CREATE INDEX idx_orders_covering
ON Orders(CustomerID, OrderDate)
INCLUDE (TotalAmount, Status);

-- Drop an index
DROP INDEX idx_lastname ON Customers;

-- Rebuild a fragmented index
ALTER INDEX idx_lastname ON Customers REBUILD;

-- Update statistics (SQL Server)
UPDATE STATISTICS Customers;

-- Analyze table (MySQL/PostgreSQL)
ANALYZE TABLE Customers;

-- View index usage (SQL Server)
SELECT * FROM sys.dm_db_index_usage_stats;

-- View missing indexes (SQL Server)
SELECT * FROM sys.dm_db_missing_index_details;

-- Check execution plan (SQL Server)
SET STATISTICS IO ON;
SET STATISTICS TIME ON;
-- Your query here
SELECT * FROM Customers WHERE LastName = 'Smith';
```

---

## 🎯 Understanding Indexes

### What is an Index?

An index is a **data structure** (typically B-Tree) that improves the speed of data retrieval operations on a database table. Think of it like a book's index - instead of reading every page, you can jump directly to the information you need.

### How Indexes Work

```sql
-- WITHOUT INDEX: Full table scan (slow)
-- Database reads EVERY row to find matches
SELECT * FROM Customers WHERE LastName = 'Smith';
-- Reads: 1,000,000 rows (if table has 1M rows)
-- Time: ~5 seconds

-- WITH INDEX on LastName: Index seek (fast)
CREATE INDEX idx_lastname ON Customers(LastName);
SELECT * FROM Customers WHERE LastName = 'Smith';
-- Reads: ~50 rows (only the matches)
-- Time: ~0.01 seconds
```

### Index Structure Visualization

```
B-Tree Index on LastName:

                    [M]
                   /   \
            [D]           [S]
           /   \         /   \
      [A-C] [E-L]   [N-R] [T-Z]
        |     |       |     |
     Data  Data    Data  Data
   Pointers        (Smith here)
```

### When Indexes Help

✅ **Good Use Cases:**
- WHERE clauses: `WHERE Status = 'Active'`
- JOIN conditions: `JOIN Orders ON c.ID = o.CustomerID`
- ORDER BY: `ORDER BY CreatedDate DESC`
- GROUP BY: `GROUP BY Category`
- Frequently searched columns
- Foreign key columns

❌ **When Indexes Don't Help:**
- Small tables (< 1000 rows)
- Columns with low cardinality (few unique values): `Gender (M/F)`
- Frequently updated columns
- Columns never used in WHERE/JOIN/ORDER BY

---

## 📚 Index Types Detailed

### 1. Clustered Index

**Definition**: Determines the physical order of data in the table. Only ONE per table.

```sql
-- Create clustered index (usually on Primary Key)
CREATE CLUSTERED INDEX idx_customer_id
ON Customers(CustomerID);

-- The table is now physically sorted by CustomerID
-- Like a dictionary sorted alphabetically
```

**Characteristics:**
- ⚡ Fastest for range queries: `WHERE ID BETWEEN 100 AND 200`
- 📊 Table data IS the index (leaf nodes contain actual data)
- 🎯 Best for: Primary keys, frequently range-searched columns
- ⚠️ Only ONE per table

**Example:**
```sql
-- Very fast with clustered index on OrderDate
SELECT * FROM Orders
WHERE OrderDate BETWEEN '2024-01-01' AND '2024-01-31'
ORDER BY OrderDate;
-- Uses clustered index seek + ordered scan
```

### 2. Non-Clustered Index

**Definition**: Separate structure with pointers to data. Can have MANY per table.

```sql
-- Create non-clustered index
CREATE NONCLUSTERED INDEX idx_email
ON Customers(Email);

-- Index structure:
-- Email (sorted) -> Pointer to actual row
```

**Characteristics:**
- 📖 Like a book index pointing to pages
- 🔢 Can have up to 999 per table (SQL Server)
- 💾 Takes additional disk space
- 🎯 Best for: Frequently searched non-PK columns

**Example:**
```sql
-- Fast lookup with non-clustered index
CREATE INDEX idx_status ON Orders(Status);

SELECT OrderID, CustomerID, OrderDate
FROM Orders
WHERE Status = 'Pending';
-- Index seek on idx_status, then lookups to get other columns
```

### 3. Unique Index

**Definition**: Ensures all values in the indexed column(s) are unique.

```sql
-- Create unique index
CREATE UNIQUE INDEX idx_unique_email
ON Customers(Email);

-- This will fail:
INSERT INTO Customers (Email) VALUES ('john@email.com');
INSERT INTO Customers (Email) VALUES ('john@email.com'); -- ERROR!

-- Composite unique index
CREATE UNIQUE INDEX idx_unique_user_product
ON Favorites(UserID, ProductID);
-- Prevents duplicate favorites
```

### 4. Covering Index (Included Columns)

**Definition**: Index that contains ALL columns needed by a query (no lookup required).

```sql
-- Without covering index: Index seek + key lookup (2 operations)
CREATE INDEX idx_customerid ON Orders(CustomerID);
SELECT CustomerID, OrderDate, TotalAmount
FROM Orders
WHERE CustomerID = 12345;
-- 1. Seek idx_customerid for CustomerID=12345
-- 2. Lookup to get OrderDate, TotalAmount (SLOW!)

-- WITH covering index: Index seek only (1 operation)
CREATE INDEX idx_customerid_covering
ON Orders(CustomerID)
INCLUDE (OrderDate, TotalAmount);
-- 1. Seek idx_customerid_covering - has everything! (FAST!)
```

**Why It's Faster:**
- ✅ All data in index (no table lookup)
- ✅ Reduces I/O operations
- ✅ Can turn key lookups into index-only scans

### 5. Filtered Index

**Definition**: Index on a subset of rows (with WHERE clause).

```sql
-- Index only active orders (smaller, faster)
CREATE INDEX idx_active_orders
ON Orders(OrderDate)
WHERE Status = 'Active';

-- Much smaller than indexing all orders
-- Perfect for queries filtering on Status='Active'
SELECT * FROM Orders
WHERE Status = 'Active' AND OrderDate > '2024-01-01';
-- Uses filtered index!
```

**Benefits:**
- 💾 Smaller index size (less disk space)
- ⚡ Faster maintenance (fewer rows to update)
- 🎯 Optimized for specific query patterns

### 6. Composite Index (Multi-Column)

**Definition**: Index on multiple columns together.

```sql
-- Create composite index
CREATE INDEX idx_name
ON Customers(LastName, FirstName, MiddleName);
```

**Column Order Matters! (Left-to-Right Rule)**

```sql
-- Index: (LastName, FirstName, MiddleName)

-- ✅ Uses index efficiently:
WHERE LastName = 'Smith'
WHERE LastName = 'Smith' AND FirstName = 'John'
WHERE LastName = 'Smith' AND FirstName = 'John' AND MiddleName = 'Paul'

-- ⚠️ Uses index less efficiently:
WHERE FirstName = 'John'  -- Skips first column (index scan)
WHERE MiddleName = 'Paul'  -- Skips first columns (table scan)

-- ❌ Cannot use index at all:
WHERE FirstName = 'John' AND MiddleName = 'Paul'  -- No LastName
```

**Optimal Column Order:**
1. **Equality first**: `WHERE Status = 'Active'`
2. **Range second**: `WHERE OrderDate > '2024-01-01'`
3. **Selectivity**: Most selective (unique values) first

```sql
-- Good composite index order
CREATE INDEX idx_orders_optimal
ON Orders(Status, CustomerID, OrderDate);
-- Status (equality, low cardinality)
-- CustomerID (equality, high cardinality)
-- OrderDate (range)

-- Usage:
SELECT * FROM Orders
WHERE Status = 'Active'      -- Uses index column 1
  AND CustomerID = 12345     -- Uses index column 2
  AND OrderDate > '2024-01-01';  -- Uses index column 3
```

### 7. Full-Text Index

**Definition**: Special index for text search in large text columns.

```sql
-- Create full-text index (SQL Server)
CREATE FULLTEXT INDEX ON Articles(Title, Content)
KEY INDEX PK_Articles;

-- Search using full-text queries
SELECT * FROM Articles
WHERE CONTAINS(Content, 'database optimization');

-- Search with ranking
SELECT *, KEY_TABLE_RANK
FROM Articles
WHERE CONTAINS(Content, 'indexing AND performance');
```

### 8. Columnstore Index

**Definition**: Columnar storage for analytical queries (data warehousing).

```sql
-- Create columnstore index
CREATE COLUMNSTORE INDEX idx_sales_columnstore
ON Sales(OrderDate, ProductID, Quantity, Revenue);

-- Excellent for aggregations
SELECT ProductID, SUM(Revenue), AVG(Quantity)
FROM Sales
GROUP BY ProductID;
-- 10-100x faster than row-based indexes!
```

**Use Cases:**
- Data warehousing
- Analytical queries (SUM, AVG, GROUP BY)
- Large fact tables
- Read-heavy workloads

---

## 🔨 Creating Indexes

### Syntax Across Different Databases

#### SQL Server
```sql
-- Basic index
CREATE INDEX idx_column ON TableName(ColumnName);

-- Clustered index
CREATE CLUSTERED INDEX idx_pk ON TableName(ID);

-- Non-clustered index with included columns
CREATE NONCLUSTERED INDEX idx_name
ON Customers(LastName, FirstName)
INCLUDE (Email, Phone);

-- Filtered index
CREATE INDEX idx_active
ON Orders(OrderDate)
WHERE Status = 'Active';

-- Index with options
CREATE INDEX idx_optimized
ON LargeTable(Column1)
WITH (FILLFACTOR = 80, ONLINE = ON);
```

#### MySQL
```sql
-- Basic index
CREATE INDEX idx_column ON TableName(ColumnName);

-- Unique index
CREATE UNIQUE INDEX idx_email ON Customers(Email);

-- Composite index
CREATE INDEX idx_name ON Customers(LastName, FirstName);

-- Full-text index
CREATE FULLTEXT INDEX idx_content ON Articles(Content);

-- Add index to existing table
ALTER TABLE Customers ADD INDEX idx_lastname (LastName);

-- Prefix index (for large strings)
CREATE INDEX idx_description ON Products(Description(100));
```

#### PostgreSQL
```sql
-- Basic B-tree index (default)
CREATE INDEX idx_column ON table_name(column_name);

-- Hash index (for equality only)
CREATE INDEX idx_hash ON table_name USING HASH (column_name);

-- GiST index (for geometric data)
CREATE INDEX idx_location ON places USING GIST (location);

-- GIN index (for arrays, JSON)
CREATE INDEX idx_tags ON articles USING GIN (tags);

-- Partial index (like filtered)
CREATE INDEX idx_active ON orders(order_date)
WHERE status = 'Active';

-- Expression index
CREATE INDEX idx_lower_email ON customers(LOWER(email));

-- Concurrent creation (doesn't lock table)
CREATE INDEX CONCURRENTLY idx_name ON customers(last_name);
```

### Index Creation Best Practices

```sql
-- 1. CREATE INDEXES DURING OFF-PEAK HOURS
-- Large tables can take hours to index

-- 2. Use ONLINE option if available (SQL Server Enterprise)
CREATE INDEX idx_name ON LargeTable(Column)
WITH (ONLINE = ON);
-- Allows concurrent queries during index creation

-- 3. Check existing indexes first
-- SQL Server
SELECT
    t.name AS TableName,
    i.name AS IndexName,
    COL_NAME(ic.object_id, ic.column_id) AS ColumnName
FROM sys.indexes i
JOIN sys.index_columns ic ON i.object_id = ic.object_id
    AND i.index_id = ic.index_id
JOIN sys.tables t ON i.object_id = t.object_id
WHERE t.name = 'Customers'
ORDER BY t.name, i.name, ic.key_ordinal;

-- 4. Avoid duplicate/overlapping indexes
-- Redundant:
CREATE INDEX idx_1 ON Orders(CustomerID);
CREATE INDEX idx_2 ON Orders(CustomerID, OrderDate);  -- Covers idx_1!
-- Keep only idx_2

-- 5. Consider FILLFACTOR for frequently updated tables
CREATE INDEX idx_name ON Customers(LastName)
WITH (FILLFACTOR = 80);
-- Leaves 20% free space for INSERTs (reduces page splits)
```

---

## 🚀 Query Optimization

### Step-by-Step Optimization Process

#### Step 1: Identify Slow Queries

```sql
-- SQL Server: Top 10 slowest queries
SELECT TOP 10
    qs.execution_count,
    qs.total_elapsed_time / 1000000.0 AS total_seconds,
    qs.total_elapsed_time / qs.execution_count / 1000.0 AS avg_ms,
    SUBSTRING(qt.text, (qs.statement_start_offset/2)+1,
        ((CASE qs.statement_end_offset
            WHEN -1 THEN DATALENGTH(qt.text)
            ELSE qs.statement_end_offset
        END - qs.statement_start_offset)/2) + 1) AS query_text
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
ORDER BY qs.total_elapsed_time DESC;

-- MySQL: Slow query log
-- Enable in my.cnf:
-- slow_query_log = 1
-- long_query_time = 2  -- Queries taking > 2 seconds

-- Check slow queries
SELECT * FROM mysql.slow_log;

-- PostgreSQL: Log slow queries
-- postgresql.conf:
-- log_min_duration_statement = 1000  -- Log queries > 1 second
```

#### Step 2: Analyze Execution Plan

```sql
-- SQL Server
SET STATISTICS IO ON;
SET STATISTICS TIME ON;
SET SHOWPLAN_ALL ON;

SELECT c.CustomerName, o.OrderDate, o.TotalAmount
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE c.Country = 'USA'
  AND o.OrderDate > '2024-01-01'
ORDER BY o.OrderDate DESC;

-- Look for:
-- ❌ Table Scan (reads entire table)
-- ❌ Index Scan (reads entire index)
-- ❌ Key Lookup (extra lookups after index seek)
-- ❌ High row counts
-- ❌ Sort operations
-- ✅ Index Seek (optimal)
-- ✅ Low row counts

-- MySQL
EXPLAIN SELECT c.CustomerName, o.OrderDate, o.TotalAmount
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE c.Country = 'USA'
  AND o.OrderDate > '2024-01-01';

-- PostgreSQL
EXPLAIN ANALYZE SELECT ...;
```

#### Step 3: Create Appropriate Indexes

```sql
-- Problem: Slow query
SELECT CustomerID, OrderDate, TotalAmount
FROM Orders
WHERE Status = 'Pending'
  AND OrderDate > '2024-01-01'
ORDER BY OrderDate DESC;

-- Execution plan shows: Table Scan (bad!)

-- Solution: Create optimized index
CREATE INDEX idx_orders_status_date
ON Orders(Status, OrderDate DESC)
INCLUDE (TotalAmount);

-- Now: Index Seek (good!) + no key lookups
-- Query time: 5000ms -> 50ms (100x faster!)
```

#### Step 4: Rewrite Query

```sql
-- BEFORE: Slow query with function on column
SELECT * FROM Customers
WHERE YEAR(BirthDate) = 1990;
-- Table scan! Index on BirthDate not used

-- AFTER: Rewrite to use index
SELECT * FROM Customers
WHERE BirthDate >= '1990-01-01'
  AND BirthDate < '1991-01-01';
-- Index seek on BirthDate!

---

-- BEFORE: OR conditions (often slow)
SELECT * FROM Products
WHERE Category = 'Electronics' OR Category = 'Computers';
-- May cause index scan

-- AFTER: Use IN (better optimization)
SELECT * FROM Products
WHERE Category IN ('Electronics', 'Computers');

---

-- BEFORE: NOT IN with subquery (slow)
SELECT * FROM Customers
WHERE CustomerID NOT IN (SELECT CustomerID FROM Orders);
-- Nested loop scan - very slow!

-- AFTER: Use LEFT JOIN with NULL check
SELECT c.* FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE o.CustomerID IS NULL;
-- Hash match - much faster!
```

### Query Optimization Techniques

#### 1. Avoid SELECT *

```sql
-- ❌ Bad: Retrieves unnecessary data
SELECT * FROM Orders WHERE OrderID = 12345;
-- Returns 20 columns, only need 3

-- ✅ Good: Select only needed columns
SELECT OrderID, CustomerID, TotalAmount
FROM Orders
WHERE OrderID = 12345;
-- Can use covering index!
```

#### 2. Use Appropriate JOIN Types

```sql
-- ❌ Bad: Implicit cross join (Cartesian product)
SELECT * FROM Customers c, Orders o
WHERE c.CustomerID = o.CustomerID;

-- ✅ Good: Explicit INNER JOIN
SELECT * FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID;

---

-- Join order matters for large tables:

-- ❌ Less optimal: Large table first
SELECT * FROM Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID
WHERE c.Country = 'USA';
-- Processes 10M orders, then filters

-- ✅ Better: Filter small table first
SELECT * FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE c.Country = 'USA';
-- Filters to 100K customers first, then joins
```

#### 3. Use EXISTS Instead of IN for Subqueries

```sql
-- ❌ Slower: IN with large subquery
SELECT * FROM Customers c
WHERE c.CustomerID IN (
    SELECT o.CustomerID FROM Orders o WHERE o.OrderDate > '2024-01-01'
);
-- Materialized subquery (may be large)

-- ✅ Faster: EXISTS (short-circuits)
SELECT * FROM Customers c
WHERE EXISTS (
    SELECT 1 FROM Orders o
    WHERE o.CustomerID = c.CustomerID
      AND o.OrderDate > '2024-01-01'
);
-- Stops searching when first match found
```

#### 4. Avoid Functions on Indexed Columns

```sql
-- ❌ Bad: Function prevents index usage
SELECT * FROM Customers
WHERE UPPER(LastName) = 'SMITH';
-- Table scan!

-- ✅ Good: Use computed column or rewrite
-- Option 1: Create computed column
ALTER TABLE Customers
ADD LastNameUpper AS UPPER(LastName) PERSISTED;
CREATE INDEX idx_lastname_upper ON Customers(LastNameUpper);

SELECT * FROM Customers WHERE LastNameUpper = 'SMITH';
-- Index seek!

-- Option 2: Store data in uppercase
UPDATE Customers SET LastName = UPPER(LastName);
SELECT * FROM Customers WHERE LastName = 'SMITH';

---

-- ❌ Bad: Date function prevents index
SELECT * FROM Orders
WHERE YEAR(OrderDate) = 2024;
-- Table scan!

-- ✅ Good: Range query
SELECT * FROM Orders
WHERE OrderDate >= '2024-01-01'
  AND OrderDate < '2025-01-01';
-- Index seek on OrderDate!
```

#### 5. Optimize LIKE Queries

```sql
-- ❌ Bad: Leading wildcard (cannot use index)
SELECT * FROM Customers
WHERE LastName LIKE '%smith%';
-- Table scan required

-- ✅ Good: Prefix search (can use index)
SELECT * FROM Customers
WHERE LastName LIKE 'Smith%';
-- Index seek!

-- For full-text search, use full-text index:
CREATE FULLTEXT INDEX idx_ft_lastname ON Customers(LastName);
SELECT * FROM Customers
WHERE CONTAINS(LastName, 'smith');
```

#### 6. Optimize Aggregations

```sql
-- ❌ Slow: Aggregation without proper index
SELECT Category, COUNT(*) AS Total, AVG(Price) AS AvgPrice
FROM Products
GROUP BY Category;
-- Full table scan + sort/group

-- ✅ Faster: Index on GROUP BY column
CREATE INDEX idx_category ON Products(Category);
-- Index scan (still reads all rows, but faster)

-- ✅ Fastest: Covering index
CREATE INDEX idx_category_covering
ON Products(Category)
INCLUDE (Price);
-- Index-only scan!

---

-- Use indexed views for frequently aggregated data (SQL Server)
CREATE VIEW vw_CategoryStats WITH SCHEMABINDING AS
SELECT Category, COUNT_BIG(*) AS Total, SUM(Price) AS TotalPrice
FROM dbo.Products
GROUP BY Category;

CREATE UNIQUE CLUSTERED INDEX idx_vw_category
ON vw_CategoryStats(Category);

-- Query is instant (pre-aggregated)
SELECT * FROM vw_CategoryStats;
```

#### 7. Limit Result Sets

```sql
-- ❌ Bad: Returns millions of rows
SELECT * FROM Orders;

-- ✅ Good: Use TOP/LIMIT
-- SQL Server
SELECT TOP 100 * FROM Orders ORDER BY OrderDate DESC;

-- MySQL/PostgreSQL
SELECT * FROM Orders ORDER BY OrderDate DESC LIMIT 100;

-- Use pagination for large result sets
-- Page 1
SELECT * FROM Orders ORDER BY OrderID OFFSET 0 ROWS FETCH NEXT 100 ROWS ONLY;
-- Page 2
SELECT * FROM Orders ORDER BY OrderID OFFSET 100 ROWS FETCH NEXT 100 ROWS ONLY;
```

#### 8. Optimize WHERE Clauses

```sql
-- ❌ Bad: OR on different columns
SELECT * FROM Orders
WHERE CustomerID = 123 OR Status = 'Pending';
-- Cannot efficiently use indexes

-- ✅ Good: Use UNION if separate indexes exist
SELECT * FROM Orders WHERE CustomerID = 123
UNION
SELECT * FROM Orders WHERE Status = 'Pending';

---

-- ❌ Bad: Implicit type conversion
SELECT * FROM Orders
WHERE OrderID = '12345';  -- OrderID is INT
-- Type conversion prevents index usage

-- ✅ Good: Use correct data type
SELECT * FROM Orders
WHERE OrderID = 12345;

---

-- ❌ Bad: NOT conditions
SELECT * FROM Orders WHERE Status <> 'Cancelled';
-- Index scan or table scan

-- ✅ Good: Positive conditions
SELECT * FROM Orders WHERE Status IN ('Pending', 'Processing', 'Shipped', 'Delivered');
-- Index seek
```

---

## 📊 Reading Execution Plans

### Understanding Execution Plan Operators

#### SQL Server Execution Plan

```sql
-- Enable execution plan
SET SHOWPLAN_TEXT ON;
-- Or use SSMS: Ctrl + M (graphical plan)

SELECT c.CustomerName, o.OrderID, o.TotalAmount
FROM Customers c
INNER JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE c.Country = 'USA'
ORDER BY o.OrderDate DESC;
```

**Common Operators (from best to worst):**

| Operator | Description | Performance | What It Means |
|----------|-------------|-------------|---------------|
| **Index Seek** | Uses index to find specific rows | ⚡⚡⚡ Excellent | Index is used efficiently |
| **Clustered Index Seek** | Seeks clustered index | ⚡⚡⚡ Excellent | PK lookup |
| **Index Scan** | Reads entire index | ⚠️ Moderate | Index exists but not optimal |
| **Clustered Index Scan** | Reads entire table | ❌ Poor | No useful index |
| **Table Scan** | Reads entire heap table | ❌ Poor | No indexes at all |
| **Key Lookup** | Extra lookup after seek | ⚠️ Moderate | Missing covering index |
| **Hash Match** | Hash join/aggregation | ⚠️ Moderate | May need better indexes |
| **Nested Loops** | Join method | ⚡/⚠️ Varies | Good for small tables |
| **Merge Join** | Sorted join | ⚡ Good | Both inputs sorted |
| **Sort** | Explicit sort operation | ❌ Expensive | Missing index on ORDER BY |

**Key Metrics to Watch:**

```sql
-- Check I/O statistics
SET STATISTICS IO ON;
SELECT * FROM Orders WHERE Status = 'Pending';
/*
Output:
Table 'Orders'. Scan count 1, logical reads 12534, physical reads 0
                               ^^^^^^^^^^^^^^^^^
                         High number = bad performance!
*/

-- Goal: Minimize logical reads
-- < 100 reads: Excellent
-- 100-1000 reads: Good
-- 1000-10000 reads: Needs optimization
-- > 10000 reads: Critical - needs immediate optimization
```

**Example: Diagnosing Bad Plan**

```sql
-- Slow query
SELECT o.OrderID, o.OrderDate, c.CustomerName
FROM Orders o
JOIN Customers c ON o.CustomerID = c.CustomerID
WHERE o.Status = 'Pending'
ORDER BY o.OrderDate DESC;

-- Execution plan shows:
-- 1. Clustered Index Scan on Orders (Cost: 60%) ❌ BAD!
--    - Reading entire table!
-- 2. Key Lookup (Cost: 30%) ❌ BAD!
--    - Extra lookups for CustomerName
-- 3. Sort (Cost: 10%) ⚠️ MODERATE
--    - Sorting results

-- Fixes:
-- 1. Create index on Status
CREATE INDEX idx_status ON Orders(Status);

-- 2. Create covering index to eliminate key lookups
CREATE INDEX idx_status_date_covering
ON Orders(Status, OrderDate DESC)
INCLUDE (CustomerID);

-- 3. Index on join column
CREATE INDEX idx_customerid ON Customers(CustomerID, CustomerName);

-- New execution plan:
-- 1. Index Seek on idx_status_date_covering (Cost: 70%) ✅ GOOD!
-- 2. Nested Loops Join (Cost: 30%) ✅ GOOD!
-- No sort needed (index already ordered)
-- Query time: 5000ms -> 20ms
```

### MySQL EXPLAIN Output

```sql
EXPLAIN SELECT * FROM Orders
WHERE Status = 'Pending'
ORDER BY OrderDate DESC;
```

**Key Columns:**

| Column | What to Look For |
|--------|------------------|
| **type** | ALL (bad), index (moderate), range (good), ref (good), eq_ref (excellent), const (excellent) |
| **possible_keys** | Indexes MySQL considered |
| **key** | Index actually used (NULL = no index) |
| **rows** | Estimated rows examined (lower is better) |
| **Extra** | Using filesort (bad), Using temporary (bad), Using index (good) |

```sql
-- Bad example output:
id | type | possible_keys | key  | rows    | Extra
1  | ALL  | NULL          | NULL | 1000000 | Using filesort
-- Scanning 1 million rows and sorting!

-- Good example output:
id | type  | possible_keys | key              | rows | Extra
1  | range | idx_status    | idx_status       | 150  | Using index
-- Using index, only 150 rows
```

### PostgreSQL EXPLAIN ANALYZE

```sql
EXPLAIN ANALYZE SELECT * FROM orders
WHERE status = 'Pending'
ORDER BY order_date DESC;
```

**Understanding Output:**

```
Seq Scan on orders  (cost=0.00..20000.00 rows=10000 width=100) (actual time=0.123..450.234 rows=12500 loops=1)
  Filter: (status = 'Pending'::text)
  Rows Removed by Filter: 987500
Planning Time: 0.123 ms
Execution Time: 451.234 ms
```

**What to Look For:**
- **Seq Scan** = Table scan (bad for large tables)
- **Index Scan** = Using index
- **Bitmap Index Scan** = Efficient for multiple conditions
- **actual time** = Real execution time
- **rows** = Estimated vs actual (big difference = outdated statistics)

---

## 🔧 Index Maintenance

### Index Fragmentation

**What is Fragmentation?**
- Data pages are not physically contiguous on disk
- Causes extra I/O operations
- Slows down range scans and sequential reads

```sql
-- SQL Server: Check fragmentation
SELECT
    OBJECT_NAME(ips.object_id) AS TableName,
    i.name AS IndexName,
    ips.index_type_desc,
    ips.avg_fragmentation_in_percent,
    ips.page_count
FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') ips
JOIN sys.indexes i ON ips.object_id = i.object_id
    AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 10
    AND ips.page_count > 1000
ORDER BY ips.avg_fragmentation_in_percent DESC;

-- Fragmentation levels:
-- < 10%: No action needed
-- 10-30%: REORGANIZE index
-- > 30%: REBUILD index
```

**Fix Fragmentation:**

```sql
-- SQL Server: Reorganize (online, less intensive)
ALTER INDEX idx_lastname ON Customers REORGANIZE;

-- Rebuild (faster, more thorough, offline by default)
ALTER INDEX idx_lastname ON Customers REBUILD;

-- Rebuild with online option (Enterprise edition)
ALTER INDEX idx_lastname ON Customers REBUILD WITH (ONLINE = ON);

-- Rebuild all indexes on a table
ALTER INDEX ALL ON Customers REBUILD;

---

-- MySQL: Optimize table (rebuilds indexes)
OPTIMIZE TABLE Customers;

-- PostgreSQL: Reindex
REINDEX INDEX idx_lastname;
REINDEX TABLE customers;  -- All indexes on table
```

### Update Statistics

Statistics tell the optimizer about data distribution. Outdated statistics cause bad execution plans.

```sql
-- SQL Server: Update statistics
UPDATE STATISTICS Customers;  -- All statistics
UPDATE STATISTICS Customers idx_lastname;  -- Specific index

-- Auto-update statistics (enable on database)
ALTER DATABASE YourDB SET AUTO_UPDATE_STATISTICS ON;

-- Check when statistics were last updated
SELECT
    OBJECT_NAME(s.object_id) AS TableName,
    s.name AS StatName,
    STATS_DATE(s.object_id, s.stats_id) AS LastUpdated
FROM sys.stats s
WHERE OBJECT_NAME(s.object_id) = 'Customers';

---

-- MySQL: Analyze table
ANALYZE TABLE Customers;

-- PostgreSQL: Analyze
ANALYZE customers;
ANALYZE;  -- All tables

-- Auto-vacuum (PostgreSQL) - updates statistics automatically
-- Check autovacuum status
SELECT * FROM pg_stat_user_tables;
```

### Missing Index Analysis

```sql
-- SQL Server: Find missing indexes
SELECT
    CONVERT(decimal(18,2), migs.user_seeks * migs.avg_total_user_cost * (migs.avg_user_impact * 0.01)) AS improvement_measure,
    'CREATE INDEX idx_' +
        REPLACE(REPLACE(REPLACE(mid.equality_columns, ', ', '_'), '[', ''), ']', '') + '_' +
        REPLACE(REPLACE(REPLACE(ISNULL(mid.inequality_columns, ''), ', ', '_'), '[', ''), ']', '') +
        ' ON ' + mid.statement +
        ' (' + ISNULL(mid.equality_columns, '') +
        CASE WHEN mid.inequality_columns IS NOT NULL THEN ',' + mid.inequality_columns ELSE '' END + ')' +
        CASE WHEN mid.included_columns IS NOT NULL THEN ' INCLUDE (' + mid.included_columns + ')' END AS create_index_statement,
    migs.user_seeks,
    migs.avg_total_user_cost,
    migs.avg_user_impact
FROM sys.dm_db_missing_index_groups mig
JOIN sys.dm_db_missing_index_group_stats migs ON mig.index_group_handle = migs.group_handle
JOIN sys.dm_db_missing_index_details mid ON mig.index_handle = mid.index_handle
WHERE CONVERT(decimal(18,2), migs.user_seeks * migs.avg_total_user_cost * (migs.avg_user_impact * 0.01)) > 1000
ORDER BY improvement_measure DESC;

-- Review suggestions carefully - don't blindly create all!
```

### Index Usage Statistics

```sql
-- SQL Server: Check which indexes are actually used
SELECT
    OBJECT_NAME(s.object_id) AS TableName,
    i.name AS IndexName,
    s.user_seeks,
    s.user_scans,
    s.user_lookups,
    s.user_updates,
    s.last_user_seek,
    s.last_user_scan
FROM sys.dm_db_index_usage_stats s
JOIN sys.indexes i ON s.object_id = i.object_id
    AND s.index_id = i.index_id
WHERE OBJECTPROPERTY(s.object_id, 'IsUserTable') = 1
ORDER BY (s.user_seeks + s.user_scans + s.user_lookups) ASC;

-- Indexes with 0 usage = candidates for removal
-- But check: Maybe used in monthly reports?

---

-- Find duplicate/overlapping indexes
SELECT
    t.name AS TableName,
    i1.name AS Index1,
    i2.name AS Index2,
    'Index1 is redundant' AS Note
FROM sys.indexes i1
JOIN sys.indexes i2 ON i1.object_id = i2.object_id
JOIN sys.tables t ON i1.object_id = t.object_id
WHERE i1.index_id < i2.index_id
    AND i1.type_desc = 'NONCLUSTERED'
    AND i2.type_desc = 'NONCLUSTERED'
    -- Add logic to compare columns (simplified here)
;
```

### Maintenance Scripts

```sql
-- SQL Server: Weekly maintenance script
USE YourDatabase;
GO

-- 1. Update statistics on all tables
EXEC sp_updatestats;

-- 2. Rebuild fragmented indexes
DECLARE @TableName NVARCHAR(255);
DECLARE @IndexName NVARCHAR(255);
DECLARE @Fragmentation FLOAT;
DECLARE @SQL NVARCHAR(MAX);

DECLARE index_cursor CURSOR FOR
SELECT
    OBJECT_NAME(ips.object_id),
    i.name,
    ips.avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'LIMITED') ips
JOIN sys.indexes i ON ips.object_id = i.object_id AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 30
    AND ips.page_count > 1000;

OPEN index_cursor;
FETCH NEXT FROM index_cursor INTO @TableName, @IndexName, @Fragmentation;

WHILE @@FETCH_STATUS = 0
BEGIN
    SET @SQL = 'ALTER INDEX ' + @IndexName + ' ON ' + @TableName + ' REBUILD';
    PRINT 'Rebuilding: ' + @TableName + '.' + @IndexName + ' (' + CAST(@Fragmentation AS VARCHAR(10)) + '% fragmented)';
    EXEC sp_executesql @SQL;

    FETCH NEXT FROM index_cursor INTO @TableName, @IndexName, @Fragmentation;
END

CLOSE index_cursor;
DEALLOCATE index_cursor;
```

---

## 🎓 Advanced Optimization Techniques

### 1. Partitioning

Partition large tables for better performance and maintenance.

```sql
-- SQL Server: Partition by date range
-- Step 1: Create partition function
CREATE PARTITION FUNCTION pf_OrderDate (DATE)
AS RANGE RIGHT FOR VALUES
    ('2023-01-01', '2023-04-01', '2023-07-01', '2023-10-01', '2024-01-01');
-- Creates 6 partitions: <2023-01-01, Q1, Q2, Q3, Q4, >=2024-01-01

-- Step 2: Create partition scheme
CREATE PARTITION SCHEME ps_OrderDate
AS PARTITION pf_OrderDate
ALL TO ([PRIMARY]);

-- Step 3: Create partitioned table
CREATE TABLE Orders_Partitioned (
    OrderID INT,
    OrderDate DATE,
    CustomerID INT,
    TotalAmount DECIMAL(10,2)
) ON ps_OrderDate(OrderDate);

-- Step 4: Create aligned index
CREATE CLUSTERED INDEX idx_orderdate
ON Orders_Partitioned(OrderDate)
ON ps_OrderDate(OrderDate);

-- Benefits:
-- - Queries on single partition are fast
-- - Maintenance per partition (rebuild one partition)
-- - Archive old partitions easily

-- Query specific partition (partition elimination)
SELECT * FROM Orders_Partitioned
WHERE OrderDate >= '2024-01-01' AND OrderDate < '2024-04-01';
-- Only scans Q1 2024 partition!

---

-- PostgreSQL: Declarative partitioning (v10+)
CREATE TABLE orders (
    order_id INT,
    order_date DATE,
    customer_id INT,
    total_amount DECIMAL(10,2)
) PARTITION BY RANGE (order_date);

-- Create partitions
CREATE TABLE orders_2023_q1 PARTITION OF orders
    FOR VALUES FROM ('2023-01-01') TO ('2023-04-01');
CREATE TABLE orders_2023_q2 PARTITION OF orders
    FOR VALUES FROM ('2023-04-01') TO ('2023-07-01');
-- ... etc

-- Indexes on partitions
CREATE INDEX idx_orders_2023_q1_date ON orders_2023_q1(order_date);
```

### 2. Materialized Views / Indexed Views

Pre-compute expensive aggregations.

```sql
-- SQL Server: Indexed view
CREATE VIEW vw_MonthlySales
WITH SCHEMABINDING  -- Required for indexed view
AS
SELECT
    YEAR(o.OrderDate) AS Year,
    MONTH(o.OrderDate) AS Month,
    p.ProductID,
    SUM(oi.Quantity) AS TotalQuantity,
    SUM(oi.LineTotal) AS TotalRevenue,
    COUNT_BIG(*) AS OrderCount  -- COUNT_BIG required
FROM dbo.Orders o
JOIN dbo.OrderItems oi ON o.OrderID = oi.OrderID
JOIN dbo.Products p ON oi.ProductID = p.ProductID
GROUP BY YEAR(o.OrderDate), MONTH(o.OrderDate), p.ProductID;
GO

-- Create clustered index (materializes the view)
CREATE UNIQUE CLUSTERED INDEX idx_vw_MonthlySales
ON vw_MonthlySales(Year, Month, ProductID);
GO

-- Query is instant (pre-aggregated data)
SELECT * FROM vw_MonthlySales WHERE Year = 2024;
-- Automatically maintained on INSERT/UPDATE/DELETE

---

-- PostgreSQL: Materialized view
CREATE MATERIALIZED VIEW mv_monthly_sales AS
SELECT
    DATE_TRUNC('month', order_date) AS month,
    product_id,
    SUM(quantity) AS total_quantity,
    SUM(line_total) AS total_revenue,
    COUNT(*) AS order_count
FROM orders o
JOIN order_items oi ON o.order_id = oi.order_id
GROUP BY DATE_TRUNC('month', order_date), product_id;

-- Create index on materialized view
CREATE INDEX idx_mv_monthly_sales ON mv_monthly_sales(month, product_id);

-- Refresh data (manual)
REFRESH MATERIALIZED VIEW mv_monthly_sales;

-- Concurrent refresh (doesn't lock)
REFRESH MATERIALIZED VIEW CONCURRENTLY mv_monthly_sales;
```

### 3. Query Hints (Use Carefully!)

Force optimizer to use specific strategy.

```sql
-- SQL Server: Force index usage
SELECT * FROM Orders WITH (INDEX(idx_status))
WHERE Status = 'Pending';

-- Force join type
SELECT * FROM Customers c
INNER HASH JOIN Orders o ON c.CustomerID = o.CustomerID;
-- Options: HASH JOIN, MERGE JOIN, LOOP JOIN

-- Force parallel execution
SELECT * FROM LargeTable
OPTION (MAXDOP 4);  -- Use 4 CPU cores

-- Recompile query (for parameter sniffing issues)
SELECT * FROM Orders WHERE OrderDate > @Date
OPTION (RECOMPILE);

---

-- MySQL: Force index
SELECT * FROM Orders FORCE INDEX (idx_status)
WHERE Status = 'Pending';

-- Ignore index
SELECT * FROM Orders IGNORE INDEX (idx_date)
WHERE OrderDate > '2024-01-01';

---

-- PostgreSQL: Disable features selectively
SET enable_seqscan = OFF;  -- Force index usage
SELECT * FROM orders WHERE status = 'Pending';
SET enable_seqscan = ON;  -- Re-enable

-- Cost adjustment
SET random_page_cost = 1.1;  -- For SSD storage
```

### 4. Computed/Generated Columns

Index on derived values.

```sql
-- SQL Server: Computed column
ALTER TABLE Customers
ADD FullName AS (FirstName + ' ' + LastName) PERSISTED;

CREATE INDEX idx_fullname ON Customers(FullName);

-- Now this query uses index:
SELECT * FROM Customers WHERE FullName = 'John Smith';

---

-- Computed column for complex calculation
ALTER TABLE Orders
ADD TaxAmount AS (TotalAmount * 0.08) PERSISTED;

CREATE INDEX idx_taxamount ON Orders(TaxAmount);

---

-- PostgreSQL: Generated column (v12+)
ALTER TABLE customers
ADD COLUMN full_name TEXT GENERATED ALWAYS AS (first_name || ' ' || last_name) STORED;

CREATE INDEX idx_full_name ON customers(full_name);
```

### 5. Compression

Reduce storage and improve I/O performance.

```sql
-- SQL Server: Page compression (Enterprise edition)
ALTER TABLE LargeTable REBUILD WITH (DATA_COMPRESSION = PAGE);

-- Row compression
ALTER TABLE LargeTable REBUILD WITH (DATA_COMPRESSION = ROW);

-- Check compression savings
EXEC sp_estimate_data_compression_savings
    @schema_name = 'dbo',
    @object_name = 'LargeTable',
    @index_id = NULL,
    @partition_number = NULL,
    @data_compression = 'PAGE';

---

-- MySQL: InnoDB row compression
CREATE TABLE compressed_table (
    id INT PRIMARY KEY,
    data TEXT
) ROW_FORMAT=COMPRESSED KEY_BLOCK_SIZE=8;

---

-- PostgreSQL: TOAST compression (automatic for large data)
-- Column-level compression
ALTER TABLE logs ALTER COLUMN message SET COMPRESSION pglz;
```

### 6. In-Memory Tables (SQL Server)

Ultra-fast tables in memory.

```sql
-- Enable in-memory OLTP
ALTER DATABASE YourDB
ADD FILEGROUP InMemory_FG CONTAINS MEMORY_OPTIMIZED_DATA;

ALTER DATABASE YourDB
ADD FILE (NAME='InMemory_File', FILENAME='C:\Data\InMemory.ndf')
TO FILEGROUP InMemory_FG;

-- Create memory-optimized table
CREATE TABLE SessionData (
    SessionID INT NOT NULL PRIMARY KEY NONCLUSTERED HASH WITH (BUCKET_COUNT = 1000000),
    UserID INT NOT NULL,
    Data NVARCHAR(4000),
    LastAccess DATETIME2 NOT NULL,
    INDEX idx_userid NONCLUSTERED (UserID)
) WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA);

-- 10-100x faster than disk-based tables!
-- Perfect for: Session state, temporary data, lookups
```

### 7. Read-Only Tables/Filegroups

Eliminate maintenance overhead for static data.

```sql
-- SQL Server: Read-only filegroup
ALTER DATABASE YourDB ADD FILEGROUP ArchiveFG;
ALTER DATABASE YourDB
ADD FILE (NAME='Archive', FILENAME='C:\Data\Archive.ndf')
TO FILEGROUP ArchiveFG;

-- Move old data to read-only filegroup
-- (Use partitioning to switch data)

ALTER DATABASE YourDB MODIFY FILEGROUP ArchiveFG READ_ONLY;
-- No more index maintenance needed for this data!
```

### 8. Query Store (SQL Server 2016+)

Track query performance over time.

```sql
-- Enable Query Store
ALTER DATABASE YourDB SET QUERY_STORE = ON;

-- View worst performing queries
SELECT
    q.query_id,
    qt.query_sql_text,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_logical_io_reads,
    rs.count_executions
FROM sys.query_store_query q
JOIN sys.query_store_query_text qt ON q.query_text_id = qt.query_text_id
JOIN sys.query_store_plan p ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats rs ON p.plan_id = rs.plan_id
ORDER BY rs.avg_duration DESC;

-- Force a specific execution plan (if current plan is bad)
EXEC sp_query_store_force_plan @query_id = 123, @plan_id = 456;
```

---

## 📈 Performance Monitoring

### Key Metrics to Monitor

#### 1. Query Response Time
```sql
-- SQL Server: Query stats
SELECT
    qs.execution_count,
    qs.total_elapsed_time / 1000000.0 AS total_seconds,
    qs.total_elapsed_time / qs.execution_count / 1000.0 AS avg_ms,
    qs.max_elapsed_time / 1000.0 AS max_ms,
    SUBSTRING(qt.text, (qs.statement_start_offset/2)+1,
        ((CASE qs.statement_end_offset
            WHEN -1 THEN DATALENGTH(qt.text)
            ELSE qs.statement_end_offset
        END - qs.statement_start_offset)/2) + 1) AS query_text
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
WHERE qs.total_elapsed_time / qs.execution_count > 1000000  -- > 1 second avg
ORDER BY qs.total_elapsed_time DESC;
```

#### 2. Index Usage
```sql
-- SQL Server: Unused indexes
SELECT
    OBJECT_NAME(i.object_id) AS TableName,
    i.name AS IndexName,
    i.type_desc,
    s.user_seeks,
    s.user_scans,
    s.user_lookups,
    s.user_updates
FROM sys.indexes i
LEFT JOIN sys.dm_db_index_usage_stats s
    ON i.object_id = s.object_id
    AND i.index_id = s.index_id
WHERE OBJECTPROPERTY(i.object_id, 'IsUserTable') = 1
    AND i.type_desc = 'NONCLUSTERED'
    AND (s.user_seeks IS NULL OR s.user_seeks + s.user_scans + s.user_lookups = 0)
    AND s.user_updates > 0;
-- These indexes cost updates but provide no benefit!
```

#### 3. Blocking/Locking
```sql
-- SQL Server: Current blocking
SELECT
    blocking.session_id AS blocking_session,
    blocked.session_id AS blocked_session,
    blocked.wait_type,
    blocked.wait_time,
    blocked.wait_resource,
    qt.text AS blocked_query
FROM sys.dm_exec_requests blocked
CROSS APPLY sys.dm_exec_sql_text(blocked.sql_handle) qt
LEFT JOIN sys.dm_exec_requests blocking
    ON blocked.blocking_session_id = blocking.session_id
WHERE blocked.blocking_session_id <> 0;
```

#### 4. I/O Statistics
```sql
-- SQL Server: I/O by table
SELECT
    OBJECT_NAME(s.object_id) AS TableName,
    i.name AS IndexName,
    s.user_seeks + s.user_scans + s.user_lookups AS total_reads,
    s.user_updates AS total_writes,
    s.leaf_insert_count,
    s.leaf_update_count,
    s.leaf_delete_count
FROM sys.dm_db_index_operational_stats(DB_ID(), NULL, NULL, NULL) s
JOIN sys.indexes i ON s.object_id = i.object_id AND s.index_id = i.index_id
WHERE OBJECTPROPERTY(s.object_id, 'IsUserTable') = 1
ORDER BY (s.user_seeks + s.user_scans + s.user_lookups) DESC;
```

#### 5. Buffer Pool Usage
```sql
-- SQL Server: Memory usage by table
SELECT
    OBJECT_NAME(p.object_id) AS TableName,
    COUNT(*) AS buffer_pages,
    COUNT(*) * 8 / 1024.0 AS buffer_mb
FROM sys.allocation_units a
JOIN sys.dm_os_buffer_descriptors b
    ON a.allocation_unit_id = b.allocation_unit_id
JOIN sys.partitions p ON a.container_id = p.hobt_id
WHERE OBJECTPROPERTY(p.object_id, 'IsUserTable') = 1
GROUP BY OBJECT_NAME(p.object_id)
ORDER BY COUNT(*) DESC;
```

### Monitoring Tools

#### SQL Server
- **SQL Server Management Studio (SSMS)**: Activity Monitor, Execution Plans
- **SQL Server Profiler**: Trace queries (deprecated, use Extended Events)
- **Extended Events**: Modern tracing
- **Query Store**: Performance history
- **Dynamic Management Views (DMVs)**: Real-time stats

#### MySQL
- **Performance Schema**: Built-in monitoring
- **Slow Query Log**: Track slow queries
- **MySQL Workbench**: GUI tool
- **pt-query-digest** (Percona Toolkit): Analyze queries

#### PostgreSQL
- **pg_stat_statements**: Query statistics extension
- **EXPLAIN ANALYZE**: Execution plans
- **pg_stat_user_tables**: Table statistics
- **pgAdmin**: GUI tool
- **pgBadger**: Log analyzer

---

## ✅ Best Practices & Common Pitfalls

### Do's ✅

1. **Start with the query**
   - Understand query patterns before creating indexes
   - Analyze execution plans before optimization
   - Create indexes based on actual usage, not guesses

2. **Index selectively**
   - Index columns used in WHERE, JOIN, ORDER BY
   - Create covering indexes for frequently run queries
   - Use composite indexes with correct column order

3. **Monitor continuously**
   - Track slow queries
   - Review index usage statistics
   - Check for missing indexes
   - Identify unused indexes

4. **Maintain regularly**
   - Rebuild fragmented indexes
   - Update statistics
   - Review and remove unused indexes
   - Archive old data

5. **Test in production-like environment**
   - Use realistic data volumes
   - Test with actual query patterns
   - Measure before and after optimization

### Don'ts ❌

1. **Don't over-index**
   ```sql
   -- ❌ Bad: Too many indexes
   CREATE INDEX idx_1 ON Orders(CustomerID);
   CREATE INDEX idx_2 ON Orders(OrderDate);
   CREATE INDEX idx_3 ON Orders(Status);
   CREATE INDEX idx_4 ON Orders(TotalAmount);
   CREATE INDEX idx_5 ON Orders(CustomerID, OrderDate);
   CREATE INDEX idx_6 ON Orders(CustomerID, Status);
   -- Every INSERT/UPDATE/DELETE maintains ALL these indexes!

   -- ✅ Good: Strategic indexes
   CREATE INDEX idx_main ON Orders(CustomerID, OrderDate) INCLUDE (Status, TotalAmount);
   CREATE INDEX idx_status ON Orders(Status) WHERE Status IN ('Pending', 'Processing');
   -- Covers most queries with minimal overhead
   ```

2. **Don't index everything**
   - Small tables (< 1000 rows) don't need indexes
   - Low cardinality columns: `Gender CHAR(1)` (only M/F)
   - Frequently updated columns without read benefit
   - Columns never used in queries

3. **Don't ignore execution plans**
   ```sql
   -- Always check the plan before and after optimization!
   SET STATISTICS IO ON;
   SET STATISTICS TIME ON;
   -- Run query and compare logical reads
   ```

4. **Don't use functions on indexed columns**
   ```sql
   -- ❌ Prevents index usage
   WHERE YEAR(OrderDate) = 2024
   WHERE UPPER(LastName) = 'SMITH'
   WHERE Amount * 1.08 > 100  -- Function on indexed column

   -- ✅ Index-friendly
   WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01'
   WHERE LastName = 'Smith'  -- Store in correct case
   WHERE Amount > 100 / 1.08  -- Function on constant
   ```

5. **Don't blindly follow missing index suggestions**
   ```sql
   -- DMVs suggest indexes based on single queries
   -- May recommend redundant/overlapping indexes
   -- Review and consolidate recommendations
   ```

### Common Pitfalls

#### 1. Parameter Sniffing (SQL Server)
```sql
-- Problem: Plan optimized for first parameter value
CREATE PROCEDURE GetOrders @CustomerID INT
AS
SELECT * FROM Orders WHERE CustomerID = @CustomerID;

-- First call: CustomerID=1 (1 order) - creates plan for 1 row
EXEC GetOrders 1;

-- Second call: CustomerID=999 (10000 orders) - uses same plan!
EXEC GetOrders 999;  -- Slow! Plan optimized for 1 row

-- Solutions:
-- 1. OPTION (RECOMPILE) - recompile each time
CREATE PROCEDURE GetOrders @CustomerID INT
AS
SELECT * FROM Orders WHERE CustomerID = @CustomerID
OPTION (RECOMPILE);

-- 2. OPTIMIZE FOR hint
SELECT * FROM Orders WHERE CustomerID = @CustomerID
OPTION (OPTIMIZE FOR (@CustomerID = 999));

-- 3. Use local variable (forces average plan)
CREATE PROCEDURE GetOrders @CustomerID INT
AS
DECLARE @CustID INT = @CustomerID;
SELECT * FROM Orders WHERE CustomerID = @CustID;
```

#### 2. Implicit Conversions
```sql
-- Problem: Index on INT column
CREATE INDEX idx_orderid ON Orders(OrderID);  -- OrderID is INT

-- Query with VARCHAR parameter
SELECT * FROM Orders WHERE OrderID = '12345';  -- VARCHAR!
-- Causes implicit conversion: CONVERT(VARCHAR, OrderID) = '12345'
-- Index not used! Table scan!

-- Solution: Use correct data type
SELECT * FROM Orders WHERE OrderID = 12345;  -- INT
```

#### 3. Leading Wildcards
```sql
-- ❌ Cannot use index
SELECT * FROM Customers WHERE LastName LIKE '%smith%';
-- Table scan required

-- ✅ Can use index
SELECT * FROM Customers WHERE LastName LIKE 'Smith%';
-- Index seek!

-- For full-text search, use full-text index
CREATE FULLTEXT INDEX idx_ft ON Customers(LastName);
SELECT * FROM Customers WHERE CONTAINS(LastName, 'smith');
```

#### 4. OR vs UNION
```sql
-- ❌ OR may cause index scan
SELECT * FROM Orders
WHERE CustomerID = 123 OR Status = 'Pending';
-- May not efficiently use indexes

-- ✅ UNION can use separate indexes
SELECT * FROM Orders WHERE CustomerID = 123
UNION
SELECT * FROM Orders WHERE Status = 'Pending';
-- Each query uses its own index!
```

#### 5. Large IN Lists
```sql
-- ❌ Slow with large IN list
SELECT * FROM Orders
WHERE OrderID IN (1,2,3,4,5,...,10000);  -- 10000 values!
-- Plan bloat, slow compilation

-- ✅ Use temp table or table parameter
CREATE TABLE #OrderIDs (OrderID INT PRIMARY KEY);
INSERT INTO #OrderIDs VALUES (1), (2), (3), ...;

SELECT o.* FROM Orders o
JOIN #OrderIDs t ON o.OrderID = t.OrderID;
-- Much faster!
```

### Index Design Checklist

Before creating an index, ask:

- [ ] Is this query slow? (Measure first!)
- [ ] Does an execution plan show a table scan or index scan?
- [ ] Is the table large enough to benefit? (> 1000 rows)
- [ ] Are the columns selective enough? (many unique values)
- [ ] Will this index be used by multiple queries?
- [ ] Is there an existing index that could be modified instead?
- [ ] What's the write vs read ratio? (Heavy writes = fewer indexes)
- [ ] Can I create a covering index to eliminate key lookups?
- [ ] Is the column order optimal for composite indexes?
- [ ] Have I tested with production-like data volume?

---

## 📚 Quick Reference Checklist

### Daily Monitoring
- [ ] Check for slow queries (> 1 second)
- [ ] Review blocking/deadlocks
- [ ] Monitor disk I/O and CPU

### Weekly Tasks
- [ ] Review missing index recommendations
- [ ] Check index fragmentation
- [ ] Identify unused indexes
- [ ] Update statistics on large tables

### Monthly Tasks
- [ ] Analyze query patterns and adjust indexes
- [ ] Review and remove unused indexes
- [ ] Rebuild heavily fragmented indexes
- [ ] Archive old data (partitioning)
- [ ] Review overall database growth

### Before Deployment
- [ ] Test all queries with production-like data
- [ ] Verify execution plans
- [ ] Check index usage statistics
- [ ] Document index strategy
- [ ] Create indexes during off-peak hours
- [ ] Monitor performance after deployment

---

## 🎯 Summary: The Optimization Process

1. **Identify** slow queries (monitoring tools, DMVs)
2. **Analyze** execution plans (find table scans, key lookups)
3. **Create** appropriate indexes (covering, composite, filtered)
4. **Rewrite** inefficient queries (avoid functions on columns)
5. **Test** with realistic data (measure improvements)
6. **Monitor** ongoing performance (index usage, fragmentation)
7. **Maintain** regularly (rebuild, update statistics)
8. **Iterate** based on changing query patterns

---

## 💡 Key Takeaways

1. **Indexes are NOT free** - they speed up reads but slow down writes
2. **One size doesn't fit all** - optimize for your specific workload
3. **Execution plans tell the truth** - always check before optimizing
4. **Covering indexes eliminate lookups** - include all needed columns
5. **Column order matters** in composite indexes (left-to-right rule)
6. **Maintenance is essential** - fragmentation and stale statistics kill performance
7. **Monitor continuously** - query patterns change over time
8. **Test with real data** - small tables behave differently than large ones
9. **Less is more** - fewer strategic indexes beat many random ones
10. **Measure everything** - optimization without measurement is guessing

---

## 🔗 Additional Resources

### SQL Server
- [Microsoft: Index Architecture and Design Guide](https://docs.microsoft.com/sql/relational-databases/sql-server-index-design-guide)
- [Brent Ozar: SQL Server Index Fundamentals](https://www.brentozar.com/archive/2012/06/index-fundamentals/)

### MySQL
- [MySQL: Optimization and Indexes](https://dev.mysql.com/doc/refman/8.0/en/optimization-indexes.html)
- [Percona: MySQL Indexing Best Practices](https://www.percona.com/blog/2019/11/21/mysql-indexing-best-practices/)

### PostgreSQL
- [PostgreSQL: Index Types](https://www.postgresql.org/docs/current/indexes-types.html)
- [Cybertec: PostgreSQL Indexing Guide](https://www.cybertec-postgresql.com/en/postgresql-indexing-index-scan-vs-bitmap-scan-vs-sequential-scan-basics/)

### General
- [Use The Index, Luke!](https://use-the-index-luke.com/) - Database indexing guide
- [SQL Performance Explained](https://sql-performance-explained.com/) - Book by Markus Winand

---

**Last Updated**: 2025-11-18
**Author**: Database Optimization Guide
**Version**: 1.0

> Remember: The best index is the one you actually need, not the one you think you might need someday!
