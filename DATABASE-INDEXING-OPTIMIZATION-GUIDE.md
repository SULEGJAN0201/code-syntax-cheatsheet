# Database Indexing & Query Optimization - Complete Guide

## Table of Contents
1. [Index Fundamentals](#index-fundamentals)
2. [Types of Indexes](#types-of-indexes)
3. [Index Design Strategies](#index-design-strategies)
4. [Identifying Slow Queries](#identifying-slow-queries)
5. [Query Execution Plans](#query-execution-plans)
6. [Query Optimization Techniques](#query-optimization-techniques)
7. [Index Maintenance](#index-maintenance)
8. [Performance Monitoring](#performance-monitoring)
9. [Advanced Optimization Methods](#advanced-optimization-methods)
10. [Best Practices](#best-practices)
11. [Common Pitfalls & Anti-Patterns](#common-pitfalls--anti-patterns)
12. [Real-World Scenarios](#real-world-scenarios)

---

## Index Fundamentals

### What is an Index?
An index is a data structure that improves the speed of data retrieval operations on a database table at the cost of additional writes and storage space.

**Analogy**: Think of an index like a book's index - instead of reading every page to find a topic, you look it up in the index and jump directly to the relevant pages.

### How Indexes Work
- **B-Tree Structure**: Most indexes use balanced tree (B-Tree) structures
- **Leaf Nodes**: Contain actual data or pointers to data
- **Non-Leaf Nodes**: Guide the search through the tree
- **Search Complexity**: O(log n) instead of O(n) for table scans

### When Indexes Help
✅ **Good for:**
- WHERE clause filtering
- JOIN operations
- ORDER BY sorting
- GROUP BY aggregation
- MIN/MAX operations
- DISTINCT operations

❌ **Not helpful for:**
- Small tables (< 1000 rows)
- Columns with low cardinality (few distinct values)
- Tables with frequent INSERT/UPDATE/DELETE operations
- Wide result sets (returning most table rows)

---

## Types of Indexes

### 1. Clustered Index
**Definition**: Determines the physical order of data in a table. Only ONE per table.

```sql
-- Create clustered index
CREATE CLUSTERED INDEX IX_Orders_OrderDate
ON Orders(OrderDate);

-- Primary key creates clustered index by default
CREATE TABLE Customers (
    CustomerID INT PRIMARY KEY,  -- Clustered index
    Name NVARCHAR(100)
);
```

**Characteristics**:
- Actual table data is stored in the leaf nodes
- Table is physically sorted by the clustered key
- Best for: Sequential access, range queries, foreign key columns

### 2. Non-Clustered Index
**Definition**: Separate structure from table data. Can have multiple per table.

```sql
-- Create non-clustered index
CREATE NONCLUSTERED INDEX IX_Customers_Email
ON Customers(Email);

-- Multiple columns (composite index)
CREATE NONCLUSTERED INDEX IX_Orders_CustomerDate
ON Orders(CustomerID, OrderDate);
```

**Characteristics**:
- Contains index key + row locator (pointer to data)
- Doesn't affect physical data order
- Can have up to 999 per table (SQL Server)

### 3. Unique Index
**Definition**: Ensures all values in indexed column(s) are unique.

```sql
-- Unique index
CREATE UNIQUE INDEX IX_Users_Email
ON Users(Email);

-- Unique constraint (automatically creates unique index)
ALTER TABLE Users
ADD CONSTRAINT UQ_Users_Email UNIQUE (Email);
```

### 4. Filtered Index
**Definition**: Index on a subset of rows based on a filter condition.

```sql
-- Index only active orders
CREATE NONCLUSTERED INDEX IX_Orders_Active
ON Orders(OrderDate)
WHERE Status = 'Active';

-- Index non-null values
CREATE NONCLUSTERED INDEX IX_Customers_Phone
ON Customers(PhoneNumber)
WHERE PhoneNumber IS NOT NULL;
```

**Benefits**:
- Smaller index size
- Reduced maintenance cost
- Better performance for specific queries

### 5. Covering Index (Included Columns)
**Definition**: Index that contains all columns needed by a query.

```sql
-- Without covering
CREATE INDEX IX_Orders_CustomerID
ON Orders(CustomerID);

-- With covering (INCLUDE clause)
CREATE INDEX IX_Orders_CustomerID
ON Orders(CustomerID)
INCLUDE (OrderDate, TotalAmount, Status);
```

**Benefits**:
- Query satisfied entirely from index (no table lookup)
- Dramatic performance improvement
- Avoid expensive key lookups

### 6. Composite Index (Multi-Column)
**Definition**: Index on multiple columns.

```sql
-- Composite index
CREATE INDEX IX_Orders_CustomerDateStatus
ON Orders(CustomerID, OrderDate, Status);
```

**Key Rules**:
- **Column Order Matters!** Most selective column first
- Supports left-prefix queries
- Example: Index on (A, B, C) supports:
  - WHERE A = x ✅
  - WHERE A = x AND B = y ✅
  - WHERE A = x AND B = y AND C = z ✅
  - WHERE B = y ❌ (doesn't use index efficiently)
  - WHERE C = z ❌ (doesn't use index efficiently)

### 7. Columnstore Index
**Definition**: Column-based storage for analytical queries.

```sql
-- Clustered columnstore
CREATE CLUSTERED COLUMNSTORE INDEX IX_Sales_Columnstore
ON SalesHistory;

-- Non-clustered columnstore
CREATE NONCLUSTERED COLUMNSTORE INDEX IX_Orders_Analytics
ON Orders(OrderDate, ProductID, Quantity, Amount);
```

**Best for**:
- Data warehouse / OLAP workloads
- Aggregation queries (SUM, AVG, COUNT)
- Large table scans
- High compression ratios

### 8. Full-Text Index
**Definition**: Specialized index for text searching.

```sql
-- Create full-text catalog
CREATE FULLTEXT CATALOG ftCatalog AS DEFAULT;

-- Create full-text index
CREATE FULLTEXT INDEX ON Documents(Content)
KEY INDEX PK_Documents;

-- Usage
SELECT * FROM Documents
WHERE CONTAINS(Content, 'database AND optimization');
```

### 9. Spatial Index
**Definition**: For geographic/spatial data types.

```sql
-- Spatial index
CREATE SPATIAL INDEX IX_Locations_Geo
ON Locations(GeoLocation);
```

### 10. Hash Index (In-Memory)
**Definition**: For memory-optimized tables.

```sql
-- Memory-optimized table with hash index
CREATE TABLE FastCache (
    CacheKey VARCHAR(100) NOT NULL PRIMARY KEY NONCLUSTERED HASH WITH (BUCKET_COUNT = 1000000),
    CacheValue NVARCHAR(MAX)
) WITH (MEMORY_OPTIMIZED = ON);
```

---

## Index Design Strategies

### 1. Selectivity Analysis
**Selectivity** = Number of distinct values / Total rows

```sql
-- Check selectivity
SELECT
    'CustomerID' AS ColumnName,
    COUNT(DISTINCT CustomerID) * 1.0 / COUNT(*) AS Selectivity
FROM Orders;
```

**Guidelines**:
- High selectivity (> 0.95): Excellent candidate for indexing
- Medium selectivity (0.50-0.95): Consider indexing based on query patterns
- Low selectivity (< 0.50): Generally poor candidate

### 2. Column Order in Composite Indexes

**Strategy**: Equality → Range → Include

```sql
-- Optimal order: Equality first, then range, then include
CREATE INDEX IX_Orders_Optimal
ON Orders(CustomerID, Status, OrderDate)  -- Equality, Equality, Range
INCLUDE (TotalAmount);                     -- Include for covering

-- Query that benefits
SELECT OrderDate, TotalAmount
FROM Orders
WHERE CustomerID = 123      -- Equality (first)
  AND Status = 'Shipped'    -- Equality (second)
  AND OrderDate >= '2024-01-01'  -- Range (third)
```

### 3. The 80/20 Rule
Focus indexing efforts on:
- 20% of queries that consume 80% of resources
- Most frequently executed queries
- Business-critical operations

### 4. Write-Heavy vs Read-Heavy Tables

**Read-Heavy Tables**:
```sql
-- Can afford more indexes
CREATE INDEX IX_Products_Category ON Products(Category);
CREATE INDEX IX_Products_Price ON Products(Price);
CREATE INDEX IX_Products_Brand ON Products(Brand);
CREATE INDEX IX_Products_CategoryBrand ON Products(Category, Brand);
```

**Write-Heavy Tables**:
```sql
-- Minimize indexes to reduce write overhead
CREATE UNIQUE CLUSTERED INDEX PK_Logs ON Logs(LogID);
CREATE INDEX IX_Logs_Timestamp ON Logs(Timestamp)  -- Only if necessary
INCLUDE (LogLevel, Message);  -- Cover common queries
```

---

## Identifying Slow Queries

### 1. Query Store (SQL Server 2016+)
**Best method for identifying problematic queries**

```sql
-- Enable Query Store
ALTER DATABASE YourDatabase
SET QUERY_STORE = ON;

-- Find top slow queries
SELECT
    q.query_id,
    qt.query_sql_text,
    rs.avg_duration / 1000.0 AS avg_duration_ms,
    rs.avg_logical_io_reads,
    rs.count_executions,
    rs.last_execution_time
FROM sys.query_store_query q
JOIN sys.query_store_query_text qt ON q.query_text_id = qt.query_text_id
JOIN sys.query_store_plan p ON q.query_id = p.query_id
JOIN sys.query_store_runtime_stats rs ON p.plan_id = rs.plan_id
ORDER BY rs.avg_duration DESC;

-- Find queries with high CPU
SELECT TOP 20
    qt.query_sql_text,
    rs.avg_cpu_time / 1000.0 AS avg_cpu_ms,
    rs.count_executions,
    (rs.avg_cpu_time * rs.count_executions) / 1000000.0 AS total_cpu_seconds
FROM sys.query_store_runtime_stats rs
JOIN sys.query_store_plan p ON rs.plan_id = p.plan_id
JOIN sys.query_store_query q ON p.query_id = q.query_id
JOIN sys.query_store_query_text qt ON q.query_text_id = qt.query_text_id
ORDER BY rs.avg_cpu_time DESC;
```

### 2. Dynamic Management Views (DMVs)

```sql
-- Top queries by CPU
SELECT TOP 20
    qs.execution_count,
    qs.total_worker_time / 1000 AS total_cpu_ms,
    qs.total_worker_time / qs.execution_count / 1000 AS avg_cpu_ms,
    qs.total_elapsed_time / 1000 AS total_elapsed_ms,
    qs.total_logical_reads,
    qs.total_physical_reads,
    SUBSTRING(st.text, (qs.statement_start_offset/2)+1,
        ((CASE qs.statement_end_offset
            WHEN -1 THEN DATALENGTH(st.text)
            ELSE qs.statement_end_offset
        END - qs.statement_start_offset)/2) + 1) AS query_text
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) st
ORDER BY qs.total_worker_time DESC;

-- Top queries by I/O
SELECT TOP 20
    qs.execution_count,
    qs.total_logical_reads + qs.total_logical_writes AS total_io,
    (qs.total_logical_reads + qs.total_logical_writes) / qs.execution_count AS avg_io,
    SUBSTRING(st.text, (qs.statement_start_offset/2)+1,
        ((CASE qs.statement_end_offset
            WHEN -1 THEN DATALENGTH(st.text)
            ELSE qs.statement_end_offset
        END - qs.statement_start_offset)/2) + 1) AS query_text
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) st
ORDER BY (qs.total_logical_reads + qs.total_logical_writes) DESC;

-- Missing indexes
SELECT
    OBJECT_NAME(d.object_id) AS TableName,
    d.equality_columns,
    d.inequality_columns,
    d.included_columns,
    s.avg_total_user_cost * s.avg_user_impact * (s.user_seeks + s.user_scans) AS improvement_measure,
    'CREATE INDEX IX_' + OBJECT_NAME(d.object_id) + '_' +
        REPLACE(REPLACE(REPLACE(ISNULL(d.equality_columns, ''), ', ', '_'), '[', ''), ']', '') +
        CASE WHEN d.inequality_columns IS NOT NULL THEN '_' +
            REPLACE(REPLACE(REPLACE(d.inequality_columns, ', ', '_'), '[', ''), ']', '') ELSE '' END +
    ' ON ' + d.statement + ' (' +
        ISNULL(d.equality_columns, '') +
        CASE WHEN d.equality_columns IS NOT NULL AND d.inequality_columns IS NOT NULL THEN ',' ELSE '' END +
        ISNULL(d.inequality_columns, '') + ')' +
        CASE WHEN d.included_columns IS NOT NULL THEN ' INCLUDE (' + d.included_columns + ')' ELSE '' END AS create_statement
FROM sys.dm_db_missing_index_details d
JOIN sys.dm_db_missing_index_groups g ON d.index_handle = g.index_handle
JOIN sys.dm_db_missing_index_group_stats s ON g.index_group_handle = s.group_handle
WHERE d.database_id = DB_ID()
ORDER BY improvement_measure DESC;
```

### 3. SQL Server Profiler / Extended Events

```sql
-- Create Extended Event session for slow queries
CREATE EVENT SESSION [SlowQueries] ON SERVER
ADD EVENT sqlserver.sql_batch_completed(
    ACTION(sqlserver.client_app_name,sqlserver.database_name,sqlserver.username)
    WHERE ([duration]>(1000000)))  -- > 1 second
ADD TARGET package0.event_file(SET filename=N'C:\Temp\SlowQueries.xel')
WITH (MAX_MEMORY=4096 KB,EVENT_RETENTION_MODE=ALLOW_SINGLE_EVENT_LOSS);

-- Start the session
ALTER EVENT SESSION [SlowQueries] ON SERVER STATE = START;

-- Query Extended Events
SELECT
    event_data.value('(event/@timestamp)[1]', 'DATETIME2') AS timestamp,
    event_data.value('(event/data[@name="duration"]/value)[1]', 'BIGINT') / 1000 AS duration_ms,
    event_data.value('(event/data[@name="batch_text"]/value)[1]', 'NVARCHAR(MAX)') AS query_text
FROM (
    SELECT CAST(event_data AS XML) AS event_data
    FROM sys.fn_xe_file_target_read_file('C:\Temp\SlowQueries*.xel', NULL, NULL, NULL)
) AS events
ORDER BY duration_ms DESC;
```

### 4. Execution Time Monitoring

```sql
-- Simple query timing
SET STATISTICS TIME ON;
SET STATISTICS IO ON;

-- Your query here
SELECT * FROM Orders WHERE CustomerID = 123;

SET STATISTICS TIME OFF;
SET STATISTICS IO OFF;
```

---

## Query Execution Plans

### Understanding Execution Plans
Execution plans show how SQL Server executes a query.

### How to View Execution Plans

```sql
-- Estimated plan (doesn't execute query)
SET SHOWPLAN_XML ON;
GO
SELECT * FROM Orders WHERE CustomerID = 123;
GO
SET SHOWPLAN_XML OFF;
GO

-- Actual plan (executes query)
SET STATISTICS XML ON;
SELECT * FROM Orders WHERE CustomerID = 123;
SET STATISTICS XML OFF;
```

**In SSMS**:
- **Ctrl+L**: Display Estimated Execution Plan
- **Ctrl+M**: Include Actual Execution Plan

### Key Operators to Understand

#### 1. Table Scan
```
Table Scan (Orders)
```
- **Meaning**: Reading entire table
- **Cost**: High for large tables
- **Fix**: Add appropriate index

#### 2. Index Scan
```
Index Scan (IX_Orders_CustomerID)
```
- **Meaning**: Scanning entire index
- **Cost**: Better than table scan, but still reads all index pages
- **Fix**: Consider filtered index or covering index

#### 3. Index Seek
```
Index Seek (IX_Orders_CustomerID)
```
- **Meaning**: Efficient lookup using B-Tree
- **Cost**: Low - this is what you want!
- **Excellent**: O(log n) complexity

#### 4. Key Lookup (RID Lookup)
```
Key Lookup (Clustered)
```
- **Meaning**: After index seek, looking up full row
- **Cost**: Can be expensive with many lookups
- **Fix**: Add INCLUDE columns to make covering index

#### 5. Nested Loops
```
Nested Loops (Inner Join)
```
- **Meaning**: For each row in outer table, scan inner table
- **Good for**: Small datasets, seeks on inner table
- **Bad for**: Large datasets without indexes

#### 6. Hash Match
```
Hash Match (Inner Join)
```
- **Meaning**: Build hash table from one input, probe with other
- **Good for**: Large datasets, no indexes
- **Memory intensive**

#### 7. Merge Join
```
Merge Join (Inner Join)
```
- **Meaning**: Both inputs sorted, merged together
- **Good for**: Large sorted datasets
- **Best performance** when data already sorted

### Analyzing Plans - What to Look For

#### 1. High-Cost Operations
```sql
-- Look for operators with high cost percentage
-- Anything > 20% of query cost needs investigation
```

#### 2. Warnings (Yellow exclamation marks)
- **Missing index**
- **Implicit conversions**
- **Missing statistics**
- **Spills to tempdb**

#### 3. Thick Arrows
- Indicate large row counts
- May indicate missing WHERE filters or bad estimates

#### 4. Scans vs Seeks
```
✅ Index Seek - Good (selective)
⚠️  Index Scan - Investigate (may be okay for small tables)
❌ Table Scan - Bad (for large tables)
```

### Example Plan Analysis

```sql
-- Bad query (likely table scan)
SELECT * FROM Orders WHERE YEAR(OrderDate) = 2024;

-- Execution Plan shows:
-- Table Scan (Orders) - Cost: 100%

-- Why? Function on column prevents index usage
```

```sql
-- Fixed query (index seek)
SELECT * FROM Orders
WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01';

-- Execution Plan shows:
-- Index Seek (IX_Orders_OrderDate) - Cost: 5%
```

### Plan Cache Analysis

```sql
-- Find plans with table scans
SELECT
    cp.objtype,
    cp.usecounts,
    qp.query_plan,
    st.text
FROM sys.dm_exec_cached_plans cp
CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle) qp
CROSS APPLY sys.dm_exec_sql_text(cp.plan_handle) st
WHERE CAST(qp.query_plan AS NVARCHAR(MAX)) LIKE '%<TableScan%'
ORDER BY cp.usecounts DESC;
```

---

## Query Optimization Techniques

### 1. Sargable Queries (Search ARGument ABLE)

**Definition**: Queries that can effectively use indexes.

#### ❌ Non-Sargable (Avoid)

```sql
-- Function on indexed column
SELECT * FROM Orders WHERE YEAR(OrderDate) = 2024;

-- String concatenation
SELECT * FROM Customers WHERE FirstName + ' ' + LastName = 'John Smith';

-- Leading wildcard
SELECT * FROM Products WHERE Name LIKE '%widget%';

-- Math on indexed column
SELECT * FROM Orders WHERE TotalAmount * 1.1 > 1000;

-- Type mismatch (implicit conversion)
SELECT * FROM Orders WHERE OrderID = '123';  -- OrderID is INT
```

#### ✅ Sargable (Use These)

```sql
-- Range on indexed column
SELECT * FROM Orders
WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01';

-- Separate column searches
SELECT * FROM Customers
WHERE FirstName = 'John' AND LastName = 'Smith';

-- Trailing wildcard (can use index)
SELECT * FROM Products WHERE Name LIKE 'widget%';

-- Move math to constant
SELECT * FROM Orders WHERE TotalAmount > 1000 / 1.1;

-- Explicit conversion
SELECT * FROM Orders WHERE OrderID = 123;  -- Or CAST('123' AS INT)
```

### 2. EXISTS vs IN

```sql
-- EXISTS - Generally faster for large subqueries
SELECT c.CustomerID, c.Name
FROM Customers c
WHERE EXISTS (
    SELECT 1 FROM Orders o WHERE o.CustomerID = c.CustomerID
);

-- IN - Can be faster for small, static lists
SELECT c.CustomerID, c.Name
FROM Customers c
WHERE c.CustomerID IN (1, 2, 3, 4, 5);

-- NOT EXISTS - Usually better than NOT IN
SELECT c.CustomerID, c.Name
FROM Customers c
WHERE NOT EXISTS (
    SELECT 1 FROM Orders o WHERE o.CustomerID = c.CustomerID
);

-- NOT IN - Problematic with NULLs
SELECT c.CustomerID, c.Name
FROM Customers c
WHERE c.CustomerID NOT IN (
    SELECT CustomerID FROM Orders WHERE CustomerID IS NOT NULL  -- Must handle NULLs!
);
```

### 3. JOIN Optimization

```sql
-- Use INNER JOIN instead of WHERE clause joins
-- Bad
SELECT * FROM Orders o, Customers c WHERE o.CustomerID = c.CustomerID;

-- Good
SELECT * FROM Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID;

-- Index foreign keys
CREATE INDEX IX_Orders_CustomerID ON Orders(CustomerID);

-- Small table first in LEFT JOIN
SELECT * FROM SmallTable st
LEFT JOIN LargeTable lt ON st.ID = lt.SmallTableID;
```

### 4. SELECT Only Needed Columns

```sql
-- Bad - Returns all columns, prevents covering index usage
SELECT * FROM Orders WHERE CustomerID = 123;

-- Good - Only needed columns, can use covering index
SELECT OrderID, OrderDate, TotalAmount
FROM Orders
WHERE CustomerID = 123;

-- Even better - Covering index
CREATE INDEX IX_Orders_CustomerID
ON Orders(CustomerID)
INCLUDE (OrderID, OrderDate, TotalAmount);
```

### 5. Avoid Cursors - Use Set-Based Operations

```sql
-- ❌ Cursor approach (SLOW)
DECLARE @OrderID INT;
DECLARE order_cursor CURSOR FOR SELECT OrderID FROM Orders;
OPEN order_cursor;
FETCH NEXT FROM order_cursor INTO @OrderID;
WHILE @@FETCH_STATUS = 0
BEGIN
    UPDATE Orders SET Processed = 1 WHERE OrderID = @OrderID;
    FETCH NEXT FROM order_cursor INTO @OrderID;
END;
CLOSE order_cursor;
DEALLOCATE order_cursor;

-- ✅ Set-based approach (FAST)
UPDATE Orders SET Processed = 1;
```

### 6. Batch Large Operations

```sql
-- Instead of updating 1 million rows at once
UPDATE Orders SET Status = 'Archived' WHERE OrderDate < '2020-01-01';

-- Update in batches
DECLARE @BatchSize INT = 10000;
DECLARE @RowsAffected INT = 1;

WHILE @RowsAffected > 0
BEGIN
    UPDATE TOP (@BatchSize) Orders
    SET Status = 'Archived'
    WHERE OrderDate < '2020-01-01'
      AND Status != 'Archived';

    SET @RowsAffected = @@ROWCOUNT;

    -- Small delay to allow other queries
    WAITFOR DELAY '00:00:01';
END;
```

### 7. Use Appropriate Data Types

```sql
-- ❌ Bad - Oversized data types
CREATE TABLE Products (
    ProductID INT,
    Code NVARCHAR(MAX),        -- Overkill for short codes
    Price DECIMAL(38,10),      -- Too much precision
    IsActive NVARCHAR(10)      -- Should be BIT
);

-- ✅ Good - Right-sized data types
CREATE TABLE Products (
    ProductID INT,
    Code VARCHAR(20),          -- Appropriate size
    Price DECIMAL(10,2),       -- Standard money precision
    IsActive BIT               -- Boolean value
);
```

### 8. Partition Large Tables

```sql
-- Create partition function
CREATE PARTITION FUNCTION PF_OrderDate (DATE)
AS RANGE RIGHT FOR VALUES
    ('2022-01-01', '2023-01-01', '2024-01-01', '2025-01-01');

-- Create partition scheme
CREATE PARTITION SCHEME PS_OrderDate
AS PARTITION PF_OrderDate
ALL TO ([PRIMARY]);

-- Create partitioned table
CREATE TABLE Orders (
    OrderID INT,
    OrderDate DATE,
    TotalAmount DECIMAL(10,2)
) ON PS_OrderDate(OrderDate);

-- Queries on specific partitions are faster
SELECT * FROM Orders WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01';
```

### 9. Use CTEs and Temporary Tables Wisely

```sql
-- CTE - Good for readability, computed once
WITH OrderSummary AS (
    SELECT
        CustomerID,
        COUNT(*) AS OrderCount,
        SUM(TotalAmount) AS TotalSpent
    FROM Orders
    GROUP BY CustomerID
)
SELECT c.Name, os.OrderCount, os.TotalSpent
FROM Customers c
JOIN OrderSummary os ON c.CustomerID = os.CustomerID;

-- Temp table - Good when reused multiple times
SELECT
    CustomerID,
    COUNT(*) AS OrderCount,
    SUM(TotalAmount) AS TotalSpent
INTO #OrderSummary
FROM Orders
GROUP BY CustomerID;

CREATE INDEX IX_Temp ON #OrderSummary(CustomerID);

SELECT c.Name, os.OrderCount, os.TotalSpent
FROM Customers c
JOIN #OrderSummary os ON c.CustomerID = os.CustomerID;

SELECT AVG(TotalSpent) FROM #OrderSummary;  -- Reuse
```

### 10. Avoid Scalar Functions in Queries

```sql
-- ❌ Scalar UDF - Executes once per row (SLOW)
CREATE FUNCTION dbo.GetCustomerTier(@CustomerID INT)
RETURNS VARCHAR(20)
AS
BEGIN
    DECLARE @Tier VARCHAR(20);
    SELECT @Tier = CASE
        WHEN SUM(TotalAmount) > 10000 THEN 'Gold'
        WHEN SUM(TotalAmount) > 5000 THEN 'Silver'
        ELSE 'Bronze'
    END
    FROM Orders WHERE CustomerID = @CustomerID;
    RETURN @Tier;
END;

-- Slow query
SELECT CustomerID, dbo.GetCustomerTier(CustomerID) AS Tier
FROM Customers;

-- ✅ Inline computation - Set-based (FAST)
SELECT
    c.CustomerID,
    CASE
        WHEN SUM(o.TotalAmount) > 10000 THEN 'Gold'
        WHEN SUM(o.TotalAmount) > 5000 THEN 'Silver'
        ELSE 'Bronze'
    END AS Tier
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID;

-- ✅ Or use inline table-valued function
CREATE FUNCTION dbo.GetCustomerTiers()
RETURNS TABLE
AS
RETURN (
    SELECT
        c.CustomerID,
        CASE
            WHEN SUM(o.TotalAmount) > 10000 THEN 'Gold'
            WHEN SUM(o.TotalAmount) > 5000 THEN 'Silver'
            ELSE 'Bronze'
        END AS Tier
    FROM Customers c
    LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
    GROUP BY c.CustomerID
);
```

---

## Index Maintenance

### 1. Index Fragmentation

**What is Fragmentation?**
- **Logical Fragmentation**: Pages out of order
- **Physical Fragmentation**: Pages not contiguous on disk
- **Page Density**: How full pages are

```sql
-- Check fragmentation
SELECT
    OBJECT_NAME(ips.object_id) AS TableName,
    i.name AS IndexName,
    ips.index_type_desc,
    ips.avg_fragmentation_in_percent,
    ips.avg_page_space_used_in_percent,
    ips.page_count
FROM sys.dm_db_index_physical_stats(DB_ID(), NULL, NULL, NULL, 'DETAILED') ips
JOIN sys.indexes i ON ips.object_id = i.object_id AND ips.index_id = i.index_id
WHERE ips.page_count > 100  -- Ignore small indexes
ORDER BY ips.avg_fragmentation_in_percent DESC;
```

### 2. Index Maintenance Strategy

```sql
-- Maintenance script
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
    WHERE ips.page_count > 100 AND i.name IS NOT NULL;

OPEN index_cursor;
FETCH NEXT FROM index_cursor INTO @TableName, @IndexName, @Fragmentation;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @Fragmentation > 30
    BEGIN
        -- Rebuild for heavy fragmentation
        SET @SQL = 'ALTER INDEX ' + @IndexName + ' ON ' + @TableName + ' REBUILD WITH (ONLINE = ON);';
        PRINT 'Rebuilding: ' + @SQL;
        -- EXEC sp_executesql @SQL;  -- Uncomment to execute
    END
    ELSE IF @Fragmentation > 10
    BEGIN
        -- Reorganize for moderate fragmentation
        SET @SQL = 'ALTER INDEX ' + @IndexName + ' ON ' + @TableName + ' REORGANIZE;';
        PRINT 'Reorganizing: ' + @SQL;
        -- EXEC sp_executesql @SQL;  -- Uncomment to execute
    END

    FETCH NEXT FROM index_cursor INTO @TableName, @IndexName, @Fragmentation;
END;

CLOSE index_cursor;
DEALLOCATE index_cursor;
```

**Guidelines**:
- **< 10% fragmentation**: No action needed
- **10-30% fragmentation**: REORGANIZE (online, less resource-intensive)
- **> 30% fragmentation**: REBUILD (offline by default, more thorough)

### 3. Update Statistics

```sql
-- Update statistics for all tables
EXEC sp_updatestats;

-- Update statistics for specific table
UPDATE STATISTICS Orders;

-- Update statistics with full scan (more accurate)
UPDATE STATISTICS Orders WITH FULLSCAN;

-- Auto update statistics (recommended setting)
ALTER DATABASE YourDatabase SET AUTO_UPDATE_STATISTICS ON;
ALTER DATABASE YourDatabase SET AUTO_UPDATE_STATISTICS_ASYNC ON;
```

### 4. Identify Unused Indexes

```sql
-- Find unused indexes (never used since last restart)
SELECT
    OBJECT_NAME(i.object_id) AS TableName,
    i.name AS IndexName,
    i.type_desc,
    us.user_seeks,
    us.user_scans,
    us.user_lookups,
    us.user_updates,
    'DROP INDEX ' + i.name + ' ON ' + OBJECT_NAME(i.object_id) AS drop_statement
FROM sys.indexes i
LEFT JOIN sys.dm_db_index_usage_stats us
    ON i.object_id = us.object_id AND i.index_id = us.index_id AND us.database_id = DB_ID()
WHERE OBJECTPROPERTY(i.object_id, 'IsUserTable') = 1
  AND i.index_id > 1  -- Exclude clustered index
  AND (us.user_seeks + us.user_scans + us.user_lookups = 0 OR us.user_seeks IS NULL)
  AND us.user_updates > 0  -- But has updates
ORDER BY us.user_updates DESC;
```

### 5. Duplicate Indexes

```sql
-- Find duplicate indexes
WITH IndexColumns AS (
    SELECT
        i.object_id,
        i.index_id,
        i.name AS IndexName,
        (
            SELECT ic.key_ordinal, c.name
            FROM sys.index_columns ic
            JOIN sys.columns c ON ic.object_id = c.object_id AND ic.column_id = c.column_id
            WHERE ic.object_id = i.object_id AND ic.index_id = i.index_id
            ORDER BY ic.key_ordinal
            FOR XML PATH('')
        ) AS IndexColumns
    FROM sys.indexes i
    WHERE i.type > 0 AND i.is_primary_key = 0 AND i.is_unique_constraint = 0
)
SELECT
    OBJECT_NAME(a.object_id) AS TableName,
    a.IndexName AS Index1,
    b.IndexName AS Index2,
    a.IndexColumns
FROM IndexColumns a
JOIN IndexColumns b ON a.object_id = b.object_id
    AND a.IndexColumns = b.IndexColumns
    AND a.index_id < b.index_id;
```

---

## Performance Monitoring

### 1. Wait Statistics

```sql
-- Clear wait stats (fresh start for analysis)
-- DBCC SQLPERF('sys.dm_os_wait_stats', CLEAR);

-- Top wait types
SELECT TOP 20
    wait_type,
    wait_time_ms / 1000.0 AS wait_time_sec,
    (wait_time_ms - signal_wait_time_ms) / 1000.0 AS resource_wait_sec,
    signal_wait_time_ms / 1000.0 AS signal_wait_sec,
    waiting_tasks_count,
    wait_time_ms * 100.0 / SUM(wait_time_ms) OVER() AS percentage
FROM sys.dm_os_wait_stats
WHERE wait_type NOT IN (
    'CLR_SEMAPHORE', 'LAZYWRITER_SLEEP', 'RESOURCE_QUEUE', 'SLEEP_TASK',
    'SLEEP_SYSTEMTASK', 'SQLTRACE_BUFFER_FLUSH', 'WAITFOR', 'XE_DISPATCHER_WAIT',
    'XE_TIMER_EVENT', 'BROKER_TO_FLUSH'
)
ORDER BY wait_time_ms DESC;
```

**Common Wait Types**:
- **PAGEIOLATCH_**: Waiting for data pages from disk (I/O bottleneck)
- **WRITELOG**: Waiting for transaction log writes (I/O bottleneck)
- **LCK_**: Lock waits (blocking/deadlocks)
- **CXPACKET**: Parallel query coordination (may need tuning)
- **SOS_SCHEDULER_YIELD**: CPU pressure

### 2. Blocking Queries

```sql
-- Current blocking
SELECT
    blocking.session_id AS blocking_session,
    blocked.session_id AS blocked_session,
    blocked_sql.text AS blocked_text,
    blocking_sql.text AS blocking_text,
    blocked.wait_type,
    blocked.wait_time,
    blocked.wait_resource
FROM sys.dm_exec_requests blocked
JOIN sys.dm_exec_requests blocking ON blocked.blocking_session_id = blocking.session_id
CROSS APPLY sys.dm_exec_sql_text(blocked.sql_handle) blocked_sql
CROSS APPLY sys.dm_exec_sql_text(blocking.sql_handle) blocking_sql;

-- Kill blocking session if needed (use carefully!)
-- KILL 54;  -- Replace with actual session_id
```

### 3. Resource Usage

```sql
-- Database size and growth
SELECT
    name,
    size * 8 / 1024 AS size_mb,
    FILEPROPERTY(name, 'SpaceUsed') * 8 / 1024 AS used_mb,
    (size - FILEPROPERTY(name, 'SpaceUsed')) * 8 / 1024 AS free_mb
FROM sys.database_files;

-- Table sizes
SELECT
    t.NAME AS TableName,
    p.rows AS RowCounts,
    SUM(a.total_pages) * 8 / 1024 AS TotalSpaceMB,
    SUM(a.used_pages) * 8 / 1024 AS UsedSpaceMB,
    (SUM(a.total_pages) - SUM(a.used_pages)) * 8 / 1024 AS UnusedSpaceMB
FROM sys.tables t
JOIN sys.indexes i ON t.OBJECT_ID = i.object_id
JOIN sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
JOIN sys.allocation_units a ON p.partition_id = a.container_id
WHERE t.is_ms_shipped = 0
GROUP BY t.Name, p.Rows
ORDER BY TotalSpaceMB DESC;
```

### 4. Index Usage Statistics

```sql
-- Index usage patterns
SELECT
    OBJECT_NAME(s.object_id) AS TableName,
    i.name AS IndexName,
    s.user_seeks,
    s.user_scans,
    s.user_lookups,
    s.user_updates,
    s.last_user_seek,
    s.last_user_scan,
    s.last_user_lookup
FROM sys.dm_db_index_usage_stats s
JOIN sys.indexes i ON s.object_id = i.object_id AND s.index_id = i.index_id
WHERE s.database_id = DB_ID()
  AND OBJECTPROPERTY(s.object_id, 'IsUserTable') = 1
ORDER BY s.user_seeks + s.user_scans + s.user_lookups DESC;
```

### 5. Performance Baseline

```sql
-- Capture baseline metrics (run periodically)
CREATE TABLE PerformanceBaseline (
    CaptureDate DATETIME DEFAULT GETDATE(),
    Metric VARCHAR(100),
    Value DECIMAL(18,2)
);

INSERT INTO PerformanceBaseline (Metric, Value)
SELECT 'AvgCPUPercent', AVG(qs.total_worker_time) / COUNT(*)
FROM sys.dm_exec_query_stats qs;

INSERT INTO PerformanceBaseline (Metric, Value)
SELECT 'AvgLogicalReads', AVG(qs.total_logical_reads) / COUNT(*)
FROM sys.dm_exec_query_stats qs;

-- Compare current to baseline
SELECT
    b.Metric,
    b.Value AS BaselineValue,
    c.Value AS CurrentValue,
    ((c.Value - b.Value) / b.Value) * 100 AS PercentChange
FROM PerformanceBaseline b
JOIN PerformanceBaseline c ON b.Metric = c.Metric
WHERE b.CaptureDate = (SELECT MIN(CaptureDate) FROM PerformanceBaseline WHERE Metric = b.Metric)
  AND c.CaptureDate = (SELECT MAX(CaptureDate) FROM PerformanceBaseline WHERE Metric = c.Metric);
```

---

## Advanced Optimization Methods

### 1. Query Hints

```sql
-- Force index usage
SELECT * FROM Orders WITH (INDEX(IX_Orders_CustomerID))
WHERE CustomerID = 123;

-- Force table scan (when optimizer chooses wrong plan)
SELECT * FROM Orders WITH (INDEX(0))
WHERE Status = 'Pending';

-- NOLOCK (read uncommitted - use carefully!)
SELECT * FROM Orders WITH (NOLOCK)
WHERE OrderDate > '2024-01-01';

-- Force order of joins
SELECT * FROM Orders o
INNER LOOP JOIN Customers c ON o.CustomerID = c.CustomerID
OPTION (FORCE ORDER);

-- Max degree of parallelism
SELECT * FROM LargeTable
WHERE ComplexCondition = 1
OPTION (MAXDOP 4);

-- Recompile (for queries with varying parameters)
SELECT * FROM Orders
WHERE OrderDate BETWEEN @StartDate AND @EndDate
OPTION (RECOMPILE);
```

**Warning**: Query hints should be last resort. Let the optimizer do its job first.

### 2. Indexed Views (Materialized Views)

```sql
-- Create indexed view
CREATE VIEW dbo.vw_CustomerOrderSummary
WITH SCHEMABINDING
AS
SELECT
    c.CustomerID,
    COUNT_BIG(*) AS OrderCount,
    SUM(o.TotalAmount) AS TotalSpent
FROM dbo.Customers c
JOIN dbo.Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID;
GO

-- Create unique clustered index (materializes the view)
CREATE UNIQUE CLUSTERED INDEX IX_CustomerOrderSummary
ON dbo.vw_CustomerOrderSummary(CustomerID);
GO

-- Query uses the indexed view automatically
SELECT CustomerID, OrderCount, TotalSpent
FROM dbo.vw_CustomerOrderSummary
WHERE CustomerID = 123;
```

**Benefits**:
- Pre-computed aggregates
- Faster than repeated calculations
- Automatically maintained

**Drawbacks**:
- Must use SCHEMABINDING
- Limited functionality (no OUTER JOIN, subqueries, etc.)
- Overhead on writes

### 3. Computed Columns with Indexes

```sql
-- Create computed column
ALTER TABLE Customers
ADD FullName AS (FirstName + ' ' + LastName) PERSISTED;

-- Index the computed column
CREATE INDEX IX_Customers_FullName ON Customers(FullName);

-- Query can use the index
SELECT * FROM Customers WHERE FullName = 'John Smith';
```

### 4. Data Compression

```sql
-- Row compression
ALTER TABLE Orders REBUILD WITH (DATA_COMPRESSION = ROW);

-- Page compression (higher compression ratio)
ALTER TABLE OrderHistory REBUILD WITH (DATA_COMPRESSION = PAGE);

-- Compression on index
CREATE INDEX IX_Orders_CustomerID
ON Orders(CustomerID)
WITH (DATA_COMPRESSION = PAGE);

-- Check compression savings
EXEC sp_estimate_data_compression_savings 'dbo', 'Orders', NULL, NULL, 'PAGE';
```

**Benefits**:
- Reduced storage
- Reduced I/O
- Better buffer pool efficiency

**Drawbacks**:
- CPU overhead for compression/decompression
- Not recommended for OLTP with high write rates

### 5. Memory-Optimized Tables (In-Memory OLTP)

```sql
-- Add memory-optimized filegroup
ALTER DATABASE YourDatabase
ADD FILEGROUP MemoryOptimized_FG CONTAINS MEMORY_OPTIMIZED_DATA;

ALTER DATABASE YourDatabase
ADD FILE (NAME='MemoryOptimized_File', FILENAME='C:\Data\MemoryOptimized')
TO FILEGROUP MemoryOptimized_FG;

-- Create memory-optimized table
CREATE TABLE dbo.SessionState (
    SessionID UNIQUEIDENTIFIER PRIMARY KEY NONCLUSTERED HASH WITH (BUCKET_COUNT = 1000000),
    UserID INT NOT NULL,
    LastActivity DATETIME2 NOT NULL,
    Data NVARCHAR(MAX),
    INDEX IX_UserID NONCLUSTERED (UserID)
) WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA);

-- Natively compiled stored procedure
CREATE PROCEDURE dbo.GetSessionState
    @SessionID UNIQUEIDENTIFIER
WITH NATIVE_COMPILATION, SCHEMABINDING
AS
BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = 'English')
    SELECT UserID, LastActivity, Data
    FROM dbo.SessionState
    WHERE SessionID = @SessionID;
END;
```

**Use Cases**:
- High-concurrency scenarios
- Low-latency requirements
- Session state
- Staging/temp tables
- Real-time analytics

### 6. Query Store Forced Plans

```sql
-- Force a specific plan for a query
EXEC sp_query_store_force_plan @query_id = 123, @plan_id = 456;

-- Unforce plan
EXEC sp_query_store_unforce_plan @query_id = 123, @plan_id = 456;

-- View forced plans
SELECT
    q.query_id,
    qt.query_sql_text,
    p.plan_id,
    p.is_forced_plan,
    p.force_failure_count
FROM sys.query_store_plan p
JOIN sys.query_store_query q ON p.query_id = q.query_id
JOIN sys.query_store_query_text qt ON q.query_text_id = qt.query_text_id
WHERE p.is_forced_plan = 1;
```

### 7. Batch Mode on Rowstore

```sql
-- Add a columnstore index to enable batch mode
CREATE NONCLUSTERED COLUMNSTORE INDEX IX_Orders_Batch
ON Orders(OrderID)  -- Dummy column
WHERE OrderID = -1  -- Never true, so index is empty
WITH (DROP_EXISTING = OFF);

-- Queries now can use batch mode even on rowstore data
SELECT
    CustomerID,
    YEAR(OrderDate) AS OrderYear,
    COUNT(*) AS OrderCount,
    SUM(TotalAmount) AS TotalSpent
FROM Orders
GROUP BY CustomerID, YEAR(OrderDate);
```

---

## Best Practices

### 1. Index Design Checklist

✅ **DO**:
- Index foreign keys
- Index columns frequently used in WHERE, JOIN, ORDER BY
- Create covering indexes for critical queries
- Use filtered indexes for subset queries
- Monitor and remove unused indexes
- Include selectivity analysis in design
- Use included columns instead of adding to key
- Consider index size vs. benefit tradeoff

❌ **DON'T**:
- Index every column "just in case"
- Create too many indexes on write-heavy tables
- Ignore index maintenance
- Use wide keys (many columns or large data types)
- Index low-cardinality columns (gender, boolean, etc.)
- Duplicate indexes with different names

### 2. Query Writing Best Practices

```sql
-- ✅ Be specific with columns
SELECT CustomerID, Name, Email FROM Customers;

-- ❌ Avoid SELECT *
SELECT * FROM Customers;

-- ✅ Filter early
SELECT c.Name, o.OrderDate
FROM Customers c
JOIN (SELECT * FROM Orders WHERE OrderDate >= '2024-01-01') o
  ON c.CustomerID = o.CustomerID;

-- ✅ Use EXISTS for existence checks
IF EXISTS (SELECT 1 FROM Orders WHERE CustomerID = @ID)
    PRINT 'Has orders';

-- ❌ Avoid COUNT for existence
IF (SELECT COUNT(*) FROM Orders WHERE CustomerID = @ID) > 0
    PRINT 'Has orders';

-- ✅ Use appropriate JOIN types
SELECT c.Name, COUNT(o.OrderID) AS OrderCount
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
GROUP BY c.CustomerID, c.Name;

-- ✅ Use UNION ALL when possible (faster than UNION)
SELECT Name FROM Customers WHERE Type = 'A'
UNION ALL
SELECT Name FROM Customers WHERE Type = 'B';
```

### 3. Database Configuration

```sql
-- Optimize tempdb
-- Multiple data files = number of CPU cores (up to 8)
ALTER DATABASE tempdb MODIFY FILE (NAME = tempdev, SIZE = 8GB, FILEGROWTH = 512MB);

-- Set realistic max server memory
sp_configure 'max server memory', 12288;  -- 12GB, leave RAM for OS
RECONFIGURE;

-- Enable optimize for ad hoc workloads
sp_configure 'optimize for ad hoc workloads', 1;
RECONFIGURE;

-- Set cost threshold for parallelism
sp_configure 'cost threshold for parallelism', 50;
RECONFIGURE;

-- Set max degree of parallelism (MAXDOP)
sp_configure 'max degree of parallelism', 4;  -- Number of cores
RECONFIGURE;
```

### 4. Transaction Management

```sql
-- Keep transactions short
BEGIN TRANSACTION;
    UPDATE Orders SET Status = 'Processed' WHERE OrderID = @ID;
COMMIT TRANSACTION;

-- Don't hold transactions during user interaction
-- ❌ Bad
BEGIN TRANSACTION;
    SELECT * FROM Orders WHERE OrderID = @ID;
    -- ... wait for user input ...
    UPDATE Orders SET Status = 'Processed' WHERE OrderID = @ID;
COMMIT TRANSACTION;

-- ✅ Good
SELECT * FROM Orders WHERE OrderID = @ID;
-- ... user interaction ...
BEGIN TRANSACTION;
    UPDATE Orders SET Status = 'Processed' WHERE OrderID = @ID;
COMMIT TRANSACTION;

-- Use appropriate isolation level
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;  -- Default, good balance
-- SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;  -- Faster but dirty reads
-- SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;  -- Safest but slowest
```

### 5. Monitoring Schedule

**Daily**:
- Check for blocking queries
- Review long-running queries
- Monitor wait statistics

**Weekly**:
- Review Query Store top queries
- Check index usage stats
- Identify missing indexes
- Review error logs

**Monthly**:
- Index maintenance (rebuild/reorganize)
- Update statistics with FULLSCAN
- Review unused indexes for removal
- Capacity planning (growth trends)

**Quarterly**:
- Review all indexes for relevance
- Benchmark performance vs. baseline
- Evaluate new indexing strategies
- Review and update documentation

---

## Common Pitfalls & Anti-Patterns

### 1. Over-Indexing

```sql
-- ❌ Too many indexes
CREATE INDEX IX1 ON Products(Category);
CREATE INDEX IX2 ON Products(Brand);
CREATE INDEX IX3 ON Products(Price);
CREATE INDEX IX4 ON Products(Category, Brand);
CREATE INDEX IX5 ON Products(Category, Price);
CREATE INDEX IX6 ON Products(Brand, Price);
CREATE INDEX IX7 ON Products(Category, Brand, Price);

-- ✅ Strategic indexing
CREATE INDEX IX_Products_CategoryBrand ON Products(Category, Brand) INCLUDE (Price);
-- This covers most use cases
```

**Problem**: Every INSERT/UPDATE/DELETE must maintain all indexes. Diminishing returns.

### 2. Ignoring Parameter Sniffing

```sql
-- Problem: Query optimized for first parameter value
CREATE PROCEDURE GetOrdersByDate
    @StartDate DATE
AS
BEGIN
    SELECT * FROM Orders WHERE OrderDate >= @StartDate;
END;

-- First call: @StartDate = '2024-01-01' (1 million rows) - creates scan plan
-- Second call: @StartDate = '2024-12-31' (10 rows) - uses scan plan (slow!)

-- ✅ Solutions:

-- Option 1: Use local variable (new plan each time)
CREATE PROCEDURE GetOrdersByDate
    @StartDate DATE
AS
BEGIN
    DECLARE @LocalDate DATE = @StartDate;
    SELECT * FROM Orders WHERE OrderDate >= @LocalDate;
END;

-- Option 2: Use RECOMPILE
CREATE PROCEDURE GetOrdersByDate
    @StartDate DATE
AS
BEGIN
    SELECT * FROM Orders WHERE OrderDate >= @StartDate
    OPTION (RECOMPILE);
END;

-- Option 3: Use OPTIMIZE FOR
CREATE PROCEDURE GetOrdersByDate
    @StartDate DATE
AS
BEGIN
    SELECT * FROM Orders WHERE OrderDate >= @StartDate
    OPTION (OPTIMIZE FOR (@StartDate = '2024-06-01'));  -- Middle value
END;
```

### 3. Implicit Conversions

```sql
-- Problem: Index not used due to type mismatch
CREATE TABLE Orders (OrderID INT PRIMARY KEY, CustomerCode VARCHAR(20));
CREATE INDEX IX_Orders_CustomerCode ON Orders(CustomerCode);

-- ❌ Bad: Implicit conversion (Index not used efficiently)
SELECT * FROM Orders WHERE CustomerCode = N'ABC123';  -- NVARCHAR literal

-- ✅ Good: Matching types
SELECT * FROM Orders WHERE CustomerCode = 'ABC123';  -- VARCHAR literal

-- Find implicit conversion warnings
SELECT
    query_plan,
    st.text
FROM sys.dm_exec_cached_plans cp
CROSS APPLY sys.dm_exec_query_plan(cp.plan_handle) qp
CROSS APPLY sys.dm_exec_sql_text(cp.plan_handle) st
WHERE CAST(query_plan AS NVARCHAR(MAX)) LIKE '%CONVERT_IMPLICIT%';
```

### 4. Incorrect Statistics

```sql
-- Check statistics age
SELECT
    OBJECT_NAME(s.object_id) AS TableName,
    s.name AS StatName,
    sp.last_updated,
    sp.rows,
    sp.rows_sampled,
    sp.modification_counter
FROM sys.stats s
CROSS APPLY sys.dm_db_stats_properties(s.object_id, s.stats_id) sp
WHERE s.object_id = OBJECT_ID('Orders')
ORDER BY sp.modification_counter DESC;

-- Fix: Update statistics
UPDATE STATISTICS Orders WITH FULLSCAN;
```

### 5. Not Using Partitioning for Very Large Tables

```sql
-- Problem: 500 million row table, queries always scan entire table

-- ✅ Solution: Partition by date
CREATE PARTITION FUNCTION PF_Year (INT)
AS RANGE RIGHT FOR VALUES (2020, 2021, 2022, 2023, 2024, 2025);

CREATE PARTITION SCHEME PS_Year
AS PARTITION PF_Year ALL TO ([PRIMARY]);

-- Partitioned table
CREATE TABLE OrderHistory (
    OrderID BIGINT,
    OrderDate DATE,
    Year AS YEAR(OrderDate) PERSISTED,
    Amount DECIMAL(10,2)
) ON PS_Year(Year);

-- Query hits only relevant partition
SELECT * FROM OrderHistory WHERE Year = 2024;
```

### 6. Inefficient Temp Table Usage

```sql
-- ❌ Bad: No indexes on temp table
SELECT o.OrderID, o.OrderDate, c.Name
INTO #TempOrders
FROM Orders o
JOIN Customers c ON o.CustomerID = c.CustomerID;

SELECT * FROM #TempOrders WHERE OrderID = 123;  -- Table scan

-- ✅ Good: Add indexes to temp tables
SELECT o.OrderID, o.OrderDate, c.Name
INTO #TempOrders
FROM Orders o
JOIN Customers c ON o.CustomerID = c.CustomerID;

CREATE INDEX IX_TempOrders_OrderID ON #TempOrders(OrderID);

SELECT * FROM #TempOrders WHERE OrderID = 123;  -- Index seek
```

### 7. Using NOLOCK Everywhere

```sql
-- ❌ Cargo cult programming
SELECT * FROM Orders WITH (NOLOCK);  -- Can return duplicate/missing rows!

-- ✅ Understand what you're doing
-- Use READ COMMITTED SNAPSHOT instead
ALTER DATABASE YourDatabase SET READ_COMMITTED_SNAPSHOT ON;

-- Now default read committed won't block writers
SELECT * FROM Orders;  -- No blocking, no dirty reads
```

---

## Real-World Scenarios

### Scenario 1: E-Commerce Order Search

**Problem**: Search orders by multiple criteria, slow performance.

```sql
-- Original slow query
SELECT * FROM Orders
WHERE (CustomerID = @CustomerID OR @CustomerID IS NULL)
  AND (Status = @Status OR @Status IS NULL)
  AND (OrderDate >= @StartDate OR @StartDate IS NULL);

-- ❌ Problem: "OR @Param IS NULL" prevents index usage

-- ✅ Solution: Dynamic SQL
CREATE PROCEDURE SearchOrders
    @CustomerID INT = NULL,
    @Status VARCHAR(20) = NULL,
    @StartDate DATE = NULL
AS
BEGIN
    DECLARE @SQL NVARCHAR(MAX) = 'SELECT * FROM Orders WHERE 1=1';

    IF @CustomerID IS NOT NULL
        SET @SQL = @SQL + ' AND CustomerID = @CustomerID';

    IF @Status IS NOT NULL
        SET @SQL = @SQL + ' AND Status = @Status';

    IF @StartDate IS NOT NULL
        SET @SQL = @SQL + ' AND OrderDate >= @StartDate';

    EXEC sp_executesql @SQL,
        N'@CustomerID INT, @Status VARCHAR(20), @StartDate DATE',
        @CustomerID, @Status, @StartDate;
END;

-- Indexes
CREATE INDEX IX_Orders_CustomerID ON Orders(CustomerID) INCLUDE (OrderDate, Status, TotalAmount);
CREATE INDEX IX_Orders_Status ON Orders(Status) INCLUDE (OrderDate, CustomerID, TotalAmount);
CREATE INDEX IX_Orders_OrderDate ON Orders(OrderDate) INCLUDE (CustomerID, Status, TotalAmount);
```

### Scenario 2: Dashboard Summary Queries

**Problem**: Dashboard loads slowly, multiple aggregate queries.

```sql
-- ❌ Original: Multiple queries
SELECT COUNT(*) FROM Orders WHERE Status = 'Pending';
SELECT COUNT(*) FROM Orders WHERE Status = 'Shipped';
SELECT SUM(TotalAmount) FROM Orders WHERE OrderDate >= @Today;
SELECT AVG(TotalAmount) FROM Orders WHERE OrderDate >= @Today;

-- ✅ Solution: Single query with GROUPING SETS or indexed view
-- Option 1: Combined query
SELECT
    COUNT(CASE WHEN Status = 'Pending' THEN 1 END) AS PendingCount,
    COUNT(CASE WHEN Status = 'Shipped' THEN 1 END) AS ShippedCount,
    SUM(CASE WHEN OrderDate >= @Today THEN TotalAmount END) AS TodayTotal,
    AVG(CASE WHEN OrderDate >= @Today THEN TotalAmount END) AS TodayAvg
FROM Orders;

-- Option 2: Indexed view for common aggregates
CREATE VIEW dbo.vw_OrderSummary
WITH SCHEMABINDING
AS
SELECT
    Status,
    CAST(OrderDate AS DATE) AS OrderDate,
    COUNT_BIG(*) AS OrderCount,
    SUM(TotalAmount) AS TotalAmount,
    SUM(TotalAmount) / COUNT_BIG(*) AS AvgAmount
FROM dbo.Orders
GROUP BY Status, CAST(OrderDate AS DATE);
GO

CREATE UNIQUE CLUSTERED INDEX IX_OrderSummary
ON dbo.vw_OrderSummary(Status, OrderDate);
GO

-- Fast dashboard query
SELECT * FROM vw_OrderSummary WHERE OrderDate = @Today;
```

### Scenario 3: Report with Multiple JOINs

**Problem**: Monthly report joins 5 tables, takes 10 minutes.

```sql
-- ❌ Original
SELECT
    c.CustomerID,
    c.Name,
    o.OrderID,
    o.OrderDate,
    p.ProductName,
    od.Quantity,
    od.Price
FROM Customers c
JOIN Orders o ON c.CustomerID = o.CustomerID
JOIN OrderDetails od ON o.OrderID = od.OrderID
JOIN Products p ON od.ProductID = p.ProductID
JOIN Categories cat ON p.CategoryID = cat.CategoryID
WHERE o.OrderDate BETWEEN @StartDate AND @EndDate;

-- ✅ Optimized approach:
-- 1. Add missing indexes
CREATE INDEX IX_Orders_CustomerIDDate ON Orders(CustomerID, OrderDate)
    INCLUDE (OrderID);
CREATE INDEX IX_OrderDetails_OrderID ON OrderDetails(OrderID)
    INCLUDE (ProductID, Quantity, Price);
CREATE INDEX IX_Products_ProductID ON Products(ProductID)
    INCLUDE (ProductName, CategoryID);

-- 2. Filter early, join later
WITH FilteredOrders AS (
    SELECT OrderID, CustomerID, OrderDate
    FROM Orders
    WHERE OrderDate BETWEEN @StartDate AND @EndDate
)
SELECT
    c.Name,
    fo.OrderDate,
    p.ProductName,
    od.Quantity,
    od.Price
FROM FilteredOrders fo
JOIN Customers c ON fo.CustomerID = c.CustomerID
JOIN OrderDetails od ON fo.OrderID = od.OrderID
JOIN Products p ON od.ProductID = p.ProductID;

-- 3. Consider summary table for monthly reports
CREATE TABLE MonthlyOrderSummary (
    ReportMonth DATE,
    CustomerID INT,
    TotalOrders INT,
    TotalAmount DECIMAL(18,2),
    INDEX IX_ReportMonth CLUSTERED (ReportMonth, CustomerID)
);

-- Populate monthly (scheduled job)
INSERT INTO MonthlyOrderSummary
SELECT
    DATEFROMPARTS(YEAR(o.OrderDate), MONTH(o.OrderDate), 1) AS ReportMonth,
    o.CustomerID,
    COUNT(*) AS TotalOrders,
    SUM(od.Quantity * od.Price) AS TotalAmount
FROM Orders o
JOIN OrderDetails od ON o.OrderID = od.OrderID
WHERE YEAR(o.OrderDate) = @Year AND MONTH(o.OrderDate) = @Month
GROUP BY YEAR(o.OrderDate), MONTH(o.OrderDate), o.CustomerID;

-- Fast report query
SELECT * FROM MonthlyOrderSummary WHERE ReportMonth = '2024-01-01';
```

### Scenario 4: Full-Text Search

**Problem**: Search product names and descriptions, LIKE queries too slow.

```sql
-- ❌ Slow LIKE search
SELECT * FROM Products
WHERE Name LIKE '%laptop%' OR Description LIKE '%laptop%';

-- ✅ Full-text search
-- Setup
CREATE FULLTEXT CATALOG ftProducts AS DEFAULT;
CREATE FULLTEXT INDEX ON Products(Name, Description)
    KEY INDEX PK_Products;

-- Fast search
SELECT * FROM Products
WHERE CONTAINS((Name, Description), 'laptop');

-- Advanced: Ranked results, multiple terms
SELECT
    ProductID,
    Name,
    RANK
FROM Products
WHERE CONTAINS((Name, Description), 'laptop OR notebook')
ORDER BY RANK DESC;

-- Fuzzy search
SELECT * FROM Products
WHERE CONTAINS(Name, 'FORMSOF(INFLECTIONAL, run)');  -- Matches: run, runs, running
```

### Scenario 5: Real-Time Analytics on OLTP Database

**Problem**: Analytical queries slow down transactional operations.

```sql
-- ✅ Solution: Columnstore index for analytics
-- Add non-clustered columnstore for analytics (doesn't affect OLTP)
CREATE NONCLUSTERED COLUMNSTORE INDEX IX_Orders_Analytics
ON Orders (OrderID, CustomerID, OrderDate, Status, TotalAmount);

-- Analytical queries use columnstore
SELECT
    YEAR(OrderDate) AS Year,
    MONTH(OrderDate) AS Month,
    Status,
    COUNT(*) AS OrderCount,
    SUM(TotalAmount) AS Revenue
FROM Orders
GROUP BY YEAR(OrderDate), MONTH(OrderDate), Status;

-- OLTP queries still use rowstore indexes
SELECT * FROM Orders WHERE OrderID = 12345;  -- Uses clustered index

-- Even better: Read-only replica for reporting
-- Configure Always On AG or read replica
-- Point reporting tools to secondary replica
```

---

## Quick Reference Cheat Sheet

### When to Create Index
```
✅ Foreign key columns
✅ Columns in WHERE clauses (high selectivity)
✅ Columns in JOIN conditions
✅ Columns in ORDER BY
✅ Columns in GROUP BY
✅ Covering indexes for critical queries
```

### Index Type Selection
```
Clustered     → Primary key, range queries, sequential access
Non-Clustered → Everything else
Unique        → Enforce uniqueness
Filtered      → Subset of data (WHERE Status = 'Active')
Covering      → Include all columns needed by query
Composite     → Multiple columns in WHERE/JOIN
Columnstore   → Analytics, aggregations, data warehouse
```

### Query Optimization Checklist
```
□ Use covering indexes
□ Write sargable queries
□ SELECT only needed columns
□ Filter early (WHERE before JOIN when possible)
□ Use EXISTS instead of IN for subqueries
□ Avoid functions on indexed columns
□ Use correct data types (avoid implicit conversion)
□ Keep transactions short
□ Batch large operations
□ Avoid cursors - use set-based operations
```

### Performance Troubleshooting Steps
```
1. Identify slow query (Query Store, DMVs)
2. View execution plan (Ctrl+M in SSMS)
3. Look for: Table Scans, Key Lookups, Warnings, High Cost ops
4. Check for: Missing indexes, implicit conversions, bad estimates
5. Implement: Add indexes, rewrite query, update statistics
6. Test: Compare before/after metrics
7. Monitor: Track improvements over time
```

### Index Maintenance Schedule
```
Daily    → Monitor blocking, check slow queries
Weekly   → Review missing indexes, check fragmentation
Monthly  → Rebuild/reorganize fragmented indexes, update stats
Quarterly→ Review index usage, remove unused indexes
```

---

## Conclusion

Database indexing and query optimization is an iterative process:

1. **Measure**: Identify slow queries and bottlenecks
2. **Analyze**: Use execution plans and statistics
3. **Optimize**: Apply appropriate indexing and query techniques
4. **Test**: Verify improvements with real workloads
5. **Monitor**: Continuously track performance
6. **Iterate**: Refine based on changing patterns

**Remember**:
- Indexes speed up reads but slow down writes
- Not every column needs an index
- Query design matters as much as indexing
- Statistics must be current for optimal plans
- Test in production-like environments
- Document your indexing strategy

**Further Learning**:
- SQL Server execution plans in depth
- Advanced query tuning techniques
- Database architecture patterns
- Monitoring and observability tools
- Cloud database optimization (Azure SQL, AWS RDS)

---

**Version**: 1.0
**Last Updated**: 2024
**Author**: Database Optimization Guide

For questions, corrections, or additions, please contribute to this living document.
