# MS SQL Server Indexing and Optimization Guide
## Complete Guide from Beginner to Advanced

> **Ultimate MS SQL Server Guide**: Master database indexing, query optimization, and performance tuning for Microsoft SQL Server. From understanding the basics to implementing advanced optimization strategies in production environments.

---

## 📚 Table of Contents

1. [Indexing Fundamentals](#indexing-fundamentals)
2. [Types of Indexes](#types-of-indexes)
3. [Creating and Managing Indexes](#creating-and-managing-indexes)
4. [5-Minute Performance Diagnostic](#5-minute-performance-diagnostic)
5. [Identifying Slow Queries](#identifying-slow-queries)
6. [Execution Plan Mastery](#execution-plan-mastery)
7. [Query Optimization Techniques](#query-optimization-techniques)
8. [Transaction Management & Locking](#transaction-management--locking)
9. [Query Plan Cache Management](#query-plan-cache-management)
10. [Index Design Methodology](#index-design-methodology)
11. [Advanced Optimization Methods](#advanced-optimization-methods)
12. [Azure SQL Optimizations](#azure-sql-optimizations)
13. [Troubleshooting Playbooks](#troubleshooting-playbooks)
14. [Real-World Scenarios](#real-world-scenarios)
15. [Error Message Decoder](#error-message-decoder)
16. [Quick Wins Checklist](#quick-wins-checklist)
17. [Best Practices](#best-practices)
18. [Performance Metrics](#performance-metrics)

---

## 🎯 Indexing Fundamentals

### 🟢 Beginner: What is an Index?

An index is a database structure that improves the speed of data retrieval operations. Think of it like a book index - instead of reading every page to find a topic, you check the index to jump directly to the right page.

```sql
-- Without index: Database scans ALL rows (Table Scan)
SELECT * FROM Users WHERE Email = 'john@example.com';
-- Scans 1,000,000 rows = SLOW ⏱️

-- With index on Email column: Database uses index (Index Seek)
CREATE INDEX IX_Users_Email ON Users(Email);
-- Finds exact row in milliseconds = FAST ⚡
```

### How Indexes Work in SQL Server

**B-Tree Structure (Default for Non-Clustered)**
```
Index on Age column:
       [50]
      /    \
   [25]    [75]
   /  \    /  \
[10][40][60][90]
  ↓   ↓   ↓   ↓
 RID RID RID RID → Points to actual data rows
```

**Key Concepts:**
- **Index Seek**: Fast lookup using index (⚡ EXCELLENT - milliseconds)
- **Index Scan**: Reading entire index (⚠️ SLOW - seconds)
- **Table Scan**: Reading entire table (❌ VERY SLOW - minutes)
- **Key Lookup**: Extra read to get columns not in index (⚠️ OVERHEAD)
- **RID Lookup**: Similar to Key Lookup for heap tables

### When to Use Indexes

✅ **CREATE indexes for:**
- Primary keys (automatic clustered index)
- Foreign keys (critical for JOINs)
- Columns in WHERE clauses (high selectivity > 95%)
- Columns in JOIN conditions
- Columns in ORDER BY clauses
- Columns frequently searched
- Columns used for filtering in reports

❌ **AVOID indexes on:**
- Small tables (< 1000 rows - table scan is faster)
- Columns with low selectivity (e.g., Gender M/F - only 2 values)
- Columns frequently updated (index maintenance overhead)
- Tables with heavy INSERT/UPDATE/DELETE (write penalty)
- Columns that are never queried
- Columns with many NULL values (unless filtered index)

### Index Performance Impact

```sql
-- Scenario: 1 million user records

-- BEFORE: No index on Email
SELECT * FROM Users WHERE Email = 'john@example.com';
-- Execution time: 2,347ms
-- Logical reads: 45,234
-- Physical reads: 1,234
-- Method: Table Scan

-- AFTER: With index on Email
CREATE INDEX IX_Users_Email ON Users(Email);
SELECT * FROM Users WHERE Email = 'john@example.com';
-- Execution time: 5ms (469x FASTER! ⚡)
-- Logical reads: 3
-- Physical reads: 0 (cached)
-- Method: Index Seek
-- Improvement: 99.8% faster, 99.99% fewer reads
```

### Index Storage and Cost

**Storage Calculation:**
```sql
-- Check index size
EXEC sp_spaceused 'Users';

-- Detailed index size by index
SELECT
    i.name AS IndexName,
    SUM(s.used_page_count) * 8 / 1024.0 AS IndexSizeMB
FROM sys.dm_db_partition_stats s
JOIN sys.indexes i ON s.object_id = i.object_id AND s.index_id = i.index_id
WHERE i.object_id = OBJECT_ID('Users')
GROUP BY i.name
ORDER BY IndexSizeMB DESC;
```

**Write Penalty:**
- Each index adds overhead to INSERT/UPDATE/DELETE
- 1 index = ~10-15% slower writes
- 5 indexes = ~40-60% slower writes
- Balance: Read performance vs. write penalty

---

## 🔍 Types of Indexes

### 🟢 Beginner: 1. Clustered Index

**One per table** - Physically sorts and stores table data.

```sql
-- Creates clustered index on ID (usually primary key)
CREATE CLUSTERED INDEX CIX_Users_ID ON Users(ID);

-- Primary key creates clustered index automatically
CREATE TABLE Users (
    ID INT PRIMARY KEY,  -- ← Clustered index created here
    Email VARCHAR(255),
    Name VARCHAR(100)
);

-- Check clustered index
EXEC sp_helpindex 'Users';
```

**Visual:**
```
Clustered Index = Actual Table Data (sorted by ID):
Page 1: [1, john@email.com, John]
        [2, jane@email.com, Jane]
        [3, bob@email.com, Bob]
Page 2: [4, alice@email.com, Alice]
        [5, dave@email.com, Dave]
```

**Characteristics:**
- ✅ Only ONE per table (the table itself IS the clustered index)
- ✅ Sorts actual table data physically
- ✅ Fastest for range queries (BETWEEN, >, <)
- ✅ No extra storage needed
- ✅ Default on PRIMARY KEY
- ⚠️ Choose wisely - affects all queries

**Best Practices:**
- Use narrow, unique, static columns (INT identity is perfect)
- Avoid GUIDs (causes fragmentation)
- Avoid frequently updated columns
- Consider business queries (date ranges work well)

### 🟢 Beginner: 2. Non-Clustered Index

**Multiple per table** - Separate structure pointing to clustered index or RID.

```sql
-- Creates non-clustered index on Email
CREATE NONCLUSTERED INDEX IX_Users_Email ON Users(Email);

-- Multiple non-clustered indexes allowed (up to 999)
CREATE INDEX IX_Users_LastName ON Users(LastName);
CREATE INDEX IX_Users_CreatedDate ON Users(CreatedDate);
CREATE INDEX IX_Users_City ON Users(City);
```

**Visual:**
```
Non-Clustered Index:    Clustered Index (Table):
[alice@email.com] → 4   Page 1: [1, john@email.com]
[bob@email.com]   → 3           [2, jane@email.com]
[dave@email.com]  → 5           [3, bob@email.com]
[jane@email.com]  → 2   Page 2: [4, alice@email.com]
[john@email.com]  → 1           [5, dave@email.com]
```

**Characteristics:**
- ✅ Up to 999 per table (SQL Server 2016+)
- ✅ Separate from table data
- ⚠️ Requires extra storage (10-20% of table size per index)
- ⚠️ Slower than clustered for ranges
- ✅ Perfect for selective lookups

### 🟢 Beginner: 3. Unique Index

Ensures column values are unique while providing fast lookups.

```sql
-- Unique index on Email (no duplicates allowed)
CREATE UNIQUE INDEX IX_Users_Email_Unique ON Users(Email);

-- Prevents duplicate inserts:
INSERT INTO Users (Email) VALUES ('john@email.com');
INSERT INTO Users (Email) VALUES ('john@email.com');
-- Error: Cannot insert duplicate key

-- UNIQUE constraint creates unique index automatically
ALTER TABLE Users ADD CONSTRAINT UQ_Email UNIQUE (Email);

-- Unique composite index
CREATE UNIQUE INDEX IX_Users_Email_TenantID
ON Users(TenantID, Email);
-- Allows same email across different tenants
```

**When to Use:**
- Natural keys (Email, SSN, EmployeeID)
- Business constraints (OrderNumber, InvoiceNumber)
- Composite uniqueness (TenantID + UserID)

### 🟡 Intermediate: 4. Composite Index (Multi-Column)

Index on multiple columns - **COLUMN ORDER MATTERS!**

```sql
-- Composite index on LastName + FirstName
CREATE INDEX IX_Users_Name ON Users(LastName, FirstName);

-- ✅ WILL USE INDEX (left-most column present)
SELECT * FROM Users WHERE LastName = 'Smith';
SELECT * FROM Users WHERE LastName = 'Smith' AND FirstName = 'John';

-- ❌ WON'T USE INDEX (missing left-most column)
SELECT * FROM Users WHERE FirstName = 'John';

-- 💡 TIP: Order by selectivity (most selective first)
CREATE INDEX IX_Orders_Search
ON Orders(CustomerID, OrderDate, Status);
-- CustomerID (high selectivity) first
```

**The Left-Most Prefix Rule:**

Index on (A, B, C) helps queries on:
- ✅ A
- ✅ A, B
- ✅ A, B, C
- ✅ A, C (partial - only A used)

But NOT helpful for:
- ❌ B
- ❌ C
- ❌ B, C

**Column Ordering Strategy:**
```sql
-- RULE 1: Equality columns first, range columns last
CREATE INDEX IX_Orders_Optimized
ON Orders(
    Status,        -- Equality (WHERE Status = 'Pending')
    CustomerID,    -- Equality (WHERE CustomerID = 123)
    OrderDate      -- Range (WHERE OrderDate > '2024-01-01')
);

-- RULE 2: Most selective column first
-- If CustomerID is unique but Status has only 5 values:
CREATE INDEX IX_Orders_Better
ON Orders(CustomerID, Status, OrderDate);
```

### 🟡 Intermediate: 5. Covering Index (Include Columns)

Index that contains ALL columns needed by query - **FASTEST!**

```sql
-- Regular index: Requires Key Lookup
CREATE INDEX IX_Users_Email ON Users(Email);

SELECT FirstName, LastName, Phone FROM Users
WHERE Email = 'john@example.com';
-- Steps:
-- 1. Index Seek on IX_Users_Email (find Email)
-- 2. Key Lookup to clustered index (get FirstName, LastName, Phone) ⚠️ SLOW
-- Cost: 0.0033 (index seek) + 0.0033 (key lookup) = 0.0066

-- Covering index: No Key Lookup needed
CREATE INDEX IX_Users_Email_Covering
ON Users(Email)
INCLUDE (FirstName, LastName, Phone);

SELECT FirstName, LastName, Phone FROM Users
WHERE Email = 'john@example.com';
-- Steps:
-- 1. Index Seek on IX_Users_Email_Covering (all data in index)
-- Cost: 0.0033 (index seek only) = 2x FASTER ⚡
```

**INCLUDE vs. Key Columns:**
```sql
-- Key columns (part of B-Tree, sorted, searchable)
CREATE INDEX IX_Example ON Table(Col1, Col2);

-- INCLUDE columns (leaf level only, not sorted, not searchable)
CREATE INDEX IX_Example ON Table(Col1, Col2) INCLUDE (Col3, Col4);

-- WHY INCLUDE?
-- 1. Smaller index (INCLUDE columns only at leaf level)
-- 2. Less fragmentation
-- 3. Better for columns not used in WHERE/JOIN/ORDER BY
```

### 🟡 Intermediate: 6. Filtered Index

Index on subset of data - **Saves space and improves performance!**

```sql
-- Only index active users (if 80% are active)
CREATE INDEX IX_Users_Active
ON Users(Email)
WHERE IsActive = 1;
-- Saves 80% index space!

-- Query must match filter to use index:
-- ✅ Uses index:
SELECT * FROM Users
WHERE Email = 'john@example.com' AND IsActive = 1;

-- ❌ Won't use index (missing IsActive = 1):
SELECT * FROM Users WHERE Email = 'john@example.com';

-- Real-world examples:
-- 1. Recent orders only (last 90 days)
CREATE INDEX IX_Orders_Recent
ON Orders(CustomerID, OrderDate)
WHERE OrderDate >= '2024-01-01';

-- 2. Non-deleted records
CREATE INDEX IX_Products_Active
ON Products(CategoryID, Price)
WHERE DeletedDate IS NULL;

-- 3. Specific status values
CREATE INDEX IX_Orders_Pending
ON Orders(OrderDate, CustomerID)
WHERE Status IN ('Pending', 'Processing');
```

**Benefits:**
- 📉 Smaller index size (50-90% reduction)
- ⚡ Faster queries (less to scan)
- 💾 Less storage cost
- 🚀 Faster index maintenance

### 🟡 Intermediate: 7. Full-Text Index

For searching large text columns (VARCHAR(MAX), NVARCHAR(MAX)).

```sql
-- Step 1: Enable full-text search
CREATE FULLTEXT CATALOG ftCatalog AS DEFAULT;

-- Step 2: Create full-text index
CREATE FULLTEXT INDEX ON Products(Description, Name)
KEY INDEX PK_Products
WITH STOPLIST = SYSTEM;

-- Search for keywords
SELECT * FROM Products
WHERE CONTAINS(Description, 'wireless bluetooth speaker');
-- Returns: "Wireless Bluetooth Speaker", "Bluetooth Wireless Audio Speaker"

-- Proximity search (words near each other)
SELECT * FROM Products
WHERE CONTAINS(Description, 'wireless NEAR speaker');

-- Inflectional forms (run, running, ran)
SELECT * FROM Products
WHERE CONTAINS(Description, 'FORMSOF(INFLECTIONAL, run)');

-- ❌ BAD: LIKE with wildcards (table scan)
SELECT * FROM Products
WHERE Description LIKE '%wireless%'
  AND Description LIKE '%bluetooth%'
  AND Description LIKE '%speaker%';
-- Execution: 5000ms

-- ✅ GOOD: Full-text search (index seek)
SELECT * FROM Products
WHERE CONTAINS(Description, 'wireless AND bluetooth AND speaker');
-- Execution: 50ms (100x FASTER)
```

### 🔴 Advanced: 8. Columnstore Index

For data warehousing and analytics (OLAP) - **10-100x compression!**

```sql
-- Nonclustered columnstore (SQL Server 2012+)
CREATE NONCLUSTERED COLUMNSTORE INDEX IX_Sales_Columnstore
ON Sales(OrderDate, ProductID, CustomerID, Amount, Quantity);

-- Clustered columnstore (SQL Server 2014+) - entire table
CREATE CLUSTERED COLUMNSTORE INDEX IX_Sales_CCI
ON Sales;

-- Perfect for analytical queries:
SELECT
    YEAR(OrderDate) as Year,
    MONTH(OrderDate) as Month,
    ProductID,
    SUM(Amount) as TotalSales,
    SUM(Quantity) as TotalQuantity,
    COUNT(*) as OrderCount
FROM Sales
WHERE OrderDate >= '2023-01-01'
GROUP BY YEAR(OrderDate), MONTH(OrderDate), ProductID
ORDER BY Year, Month, ProductID;

-- BEFORE: Row-based index
-- Execution time: 125,000ms (2+ minutes)
-- Logical reads: 4,523,234

-- AFTER: Columnstore index
-- Execution time: 1,200ms (104x FASTER! ⚡)
-- Logical reads: 45,234
-- Compression: 10x (from 50GB to 5GB)
```

**When to Use Columnstore:**
- ✅ Large fact tables (> 1 million rows)
- ✅ Analytics and aggregations (SUM, AVG, COUNT)
- ✅ Data warehousing
- ✅ Read-heavy workloads
- ❌ OLTP workloads (frequent updates)
- ❌ Small tables
- ❌ Point lookups (WHERE ID = 123)

### 🔴 Advanced: 9. Spatial Index

For geographic data (geometry, geography types).

```sql
-- Create spatial index on location data
CREATE SPATIAL INDEX IX_Stores_Location
ON Stores(Location)
WITH (BOUNDING_BOX = (xmin=-180, ymin=-90, xmax=180, ymax=90));

-- Find stores within 10 miles
DECLARE @UserLocation GEOGRAPHY =
    GEOGRAPHY::Point(47.6062, -122.3321, 4326); -- Seattle

SELECT TOP 10
    StoreName,
    Location.STDistance(@UserLocation) / 1609.34 AS DistanceMiles
FROM Stores
WHERE Location.STDistance(@UserLocation) < 16093.4 -- 10 miles in meters
ORDER BY Location.STDistance(@UserLocation);
```

### 🔴 Advanced: 10. XML Index

For XML data type columns.

```sql
-- Create primary XML index
CREATE PRIMARY XML INDEX IX_Products_Specs ON Products(SpecsXML);

-- Create secondary XML indexes
CREATE XML INDEX IX_Products_Specs_PATH ON Products(SpecsXML)
USING XML INDEX IX_Products_Specs FOR PATH;

-- Query XML data
SELECT ProductID,
       SpecsXML.value('(/specs/weight)[1]', 'DECIMAL(10,2)') AS Weight
FROM Products
WHERE SpecsXML.exist('/specs[weight > 10]') = 1;
```

---

## 🛠️ Creating and Managing Indexes

### 🟢 Beginner: Creating Indexes

**Basic Syntax:**
```sql
-- Single column index
CREATE INDEX IX_TableName_ColumnName
ON TableName(ColumnName);

-- Multi-column composite index
CREATE INDEX IX_TableName_Col1_Col2
ON TableName(Col1, Col2);

-- Unique index
CREATE UNIQUE INDEX IX_TableName_ColumnName
ON TableName(ColumnName);

-- Covering index with INCLUDE
CREATE INDEX IX_TableName_SearchCol
ON TableName(SearchColumn)
INCLUDE (Column1, Column2, Column3);

-- Filtered index
CREATE INDEX IX_TableName_FilteredCol
ON TableName(ColumnName)
WHERE SomeColumn = 'SomeValue';

-- With options
CREATE INDEX IX_TableName_ColumnName
ON TableName(ColumnName)
WITH (
    FILLFACTOR = 90,           -- Leave 10% free space
    PAD_INDEX = ON,            -- Apply fillfactor to intermediate pages
    ONLINE = ON,               -- Create without blocking (Enterprise only)
    SORT_IN_TEMPDB = ON,       -- Use tempdb for sort operations
    DATA_COMPRESSION = PAGE    -- Compress index pages
);
```

**Real-World Examples:**
```sql
-- E-commerce database optimization

-- 1. User login (unique index for fast lookup)
CREATE UNIQUE INDEX IX_Users_Email
ON Users(Email)
INCLUDE (PasswordHash, UserID, FirstName, LastName, IsActive);

-- 2. Order lookup by customer (composite index)
CREATE INDEX IX_Orders_CustomerID_OrderDate
ON Orders(CustomerID, OrderDate DESC)
INCLUDE (OrderID, TotalAmount, Status);

-- 3. Product search (covering index)
CREATE INDEX IX_Products_Search
ON Products(CategoryID, Price)
INCLUDE (ProductID, Name, Description, ImageURL, Stock);

-- 4. Recent orders only (filtered index - saves 90% space)
CREATE INDEX IX_Orders_Recent
ON Orders(CustomerID, OrderDate DESC)
WHERE OrderDate >= '2024-01-01';

-- 5. Active products only (filtered index)
CREATE INDEX IX_Products_Active
ON Products(CategoryID, Name)
WHERE IsDeleted = 0 AND IsActive = 1;
```

### 🟢 Beginner: Viewing Existing Indexes

```sql
-- Method 1: sp_helpindex (quick and easy)
EXEC sp_helpindex 'Users';

-- Method 2: Detailed index information
SELECT
    OBJECT_NAME(i.object_id) AS TableName,
    i.name AS IndexName,
    i.type_desc AS IndexType,
    i.is_unique AS IsUnique,
    i.is_primary_key AS IsPrimaryKey,
    COL_NAME(ic.object_id, ic.column_id) AS ColumnName,
    ic.index_column_id AS ColumnPosition,
    ic.is_included_column AS IsIncludedColumn
FROM sys.indexes i
INNER JOIN sys.index_columns ic
    ON i.object_id = ic.object_id
    AND i.index_id = ic.index_id
WHERE i.object_id = OBJECT_ID('Users')
ORDER BY i.name, ic.index_column_id;

-- Method 3: With size and usage statistics
SELECT
    OBJECT_NAME(i.object_id) AS TableName,
    i.name AS IndexName,
    i.type_desc AS IndexType,
    SUM(ps.used_page_count) * 8 / 1024.0 AS IndexSizeMB,
    ius.user_seeks AS Seeks,
    ius.user_scans AS Scans,
    ius.user_lookups AS Lookups,
    ius.user_updates AS Updates,
    ius.last_user_seek AS LastSeek,
    ius.last_user_scan AS LastScan
FROM sys.indexes i
LEFT JOIN sys.dm_db_partition_stats ps
    ON i.object_id = ps.object_id
    AND i.index_id = ps.index_id
LEFT JOIN sys.dm_db_index_usage_stats ius
    ON i.object_id = ius.object_id
    AND i.index_id = ius.index_id
    AND ius.database_id = DB_ID()
WHERE i.object_id = OBJECT_ID('Users')
GROUP BY i.object_id, i.name, i.type_desc,
         ius.user_seeks, ius.user_scans, ius.user_lookups,
         ius.user_updates, ius.last_user_seek, ius.last_user_scan
ORDER BY IndexSizeMB DESC;
```

### 🟢 Beginner: Dropping Indexes

```sql
-- Drop index
DROP INDEX IX_Users_Email ON Users;

-- Drop multiple indexes
DROP INDEX IX_Users_Email ON Users;
DROP INDEX IX_Users_LastName ON Users;
DROP INDEX IX_Users_City ON Users;

-- Drop if exists (SQL Server 2016+)
DROP INDEX IF EXISTS IX_Users_Email ON Users;

-- ⚠️ WARNING: Cannot drop indexes used for constraints
-- Must drop constraint first:
ALTER TABLE Users DROP CONSTRAINT UQ_Email;
```

### 🟡 Intermediate: Rebuilding and Reorganizing Indexes

**Understanding Fragmentation:**
```sql
-- Check fragmentation
SELECT
    OBJECT_NAME(ips.object_id) AS TableName,
    i.name AS IndexName,
    ips.index_type_desc AS IndexType,
    ips.avg_fragmentation_in_percent AS FragmentationPercent,
    ips.page_count AS PageCount,
    ips.avg_page_space_used_in_percent AS PageDensity
FROM sys.dm_db_index_physical_stats(
    DB_ID(),           -- Current database
    NULL,              -- All tables
    NULL,              -- All indexes
    NULL,              -- All partitions
    'LIMITED'          -- Fast scan mode
) ips
INNER JOIN sys.indexes i
    ON ips.object_id = i.object_id
    AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 10
    AND ips.page_count > 1000  -- Skip small indexes
ORDER BY ips.avg_fragmentation_in_percent DESC;
```

**Fragmentation Decision Matrix:**
```
Fragmentation Level   | Action
---------------------|------------------------
0% - 10%             | No action needed ✅
10% - 30%            | REORGANIZE ⚠️
30% - 100%           | REBUILD ❌
```

**Reorganize vs. Rebuild:**
```sql
-- REORGANIZE (online operation, doesn't lock table)
-- Use for: 10-30% fragmentation
ALTER INDEX IX_Users_Email ON Users REORGANIZE;

-- REBUILD (offline by default, faster than reorganize)
-- Use for: > 30% fragmentation
ALTER INDEX IX_Users_Email ON Users REBUILD;

-- REBUILD with options (Enterprise Edition)
ALTER INDEX IX_Users_Email ON Users REBUILD
WITH (
    ONLINE = ON,               -- Keep table accessible
    MAXDOP = 4,                -- Use 4 CPU cores
    SORT_IN_TEMPDB = ON,       -- Use tempdb for sorting
    DATA_COMPRESSION = PAGE    -- Compress pages
);

-- REBUILD ALL indexes on table
ALTER INDEX ALL ON Users REBUILD;

-- Update statistics after rebuild (automatic with REBUILD)
UPDATE STATISTICS Users;
```

**Automated Maintenance Script:**
```sql
-- Adaptive index maintenance based on fragmentation
DECLARE @TableName NVARCHAR(128) = 'Users';
DECLARE @IndexName NVARCHAR(128);
DECLARE @Fragmentation DECIMAL(5,2);

DECLARE index_cursor CURSOR FOR
SELECT
    i.name,
    ips.avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats(
    DB_ID(), OBJECT_ID(@TableName), NULL, NULL, 'LIMITED'
) ips
INNER JOIN sys.indexes i
    ON ips.object_id = i.object_id
    AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 10
    AND ips.page_count > 1000;

OPEN index_cursor;
FETCH NEXT FROM index_cursor INTO @IndexName, @Fragmentation;

WHILE @@FETCH_STATUS = 0
BEGIN
    IF @Fragmentation < 30
    BEGIN
        PRINT 'REORGANIZE: ' + @IndexName + ' (' + CAST(@Fragmentation AS VARCHAR(10)) + '%)';
        EXEC('ALTER INDEX ' + @IndexName + ' ON ' + @TableName + ' REORGANIZE');
    END
    ELSE
    BEGIN
        PRINT 'REBUILD: ' + @IndexName + ' (' + CAST(@Fragmentation AS VARCHAR(10)) + '%)';
        EXEC('ALTER INDEX ' + @IndexName + ' ON ' + @TableName + ' REBUILD');
    END

    FETCH NEXT FROM index_cursor INTO @IndexName, @Fragmentation;
END

CLOSE index_cursor;
DEALLOCATE index_cursor;
```

### 🟡 Intermediate: Disabling and Enabling Indexes

```sql
-- Disable index (useful during bulk imports)
ALTER INDEX IX_Users_Email ON Users DISABLE;

-- Check disabled indexes
SELECT
    OBJECT_NAME(object_id) AS TableName,
    name AS IndexName,
    type_desc,
    is_disabled
FROM sys.indexes
WHERE is_disabled = 1;

-- Bulk insert data (faster without indexes)
INSERT INTO Users (Name, Email, CreatedDate)
SELECT Name, Email, CreatedDate FROM StagingUsers;

-- Re-enable index (requires REBUILD)
ALTER INDEX IX_Users_Email ON Users REBUILD;

-- ⚠️ WARNING: Disabled indexes still consume space!
-- Drop if not needed
```

### Naming Conventions

**Best Practice Format:**
```sql
-- Pattern: IX_TableName_ColumnName(s)_Type

-- Single column
IX_Users_Email

-- Composite (list columns in order)
IX_Users_LastName_FirstName

-- Covering (indicate it's covering)
IX_Users_Email_Covering
IX_Users_Email_Incl  -- Alternative

-- Filtered (indicate filter condition)
IX_Users_Active
IX_Orders_Recent
IX_Products_NotDeleted

-- Unique
UQ_Users_Email
UQ_Orders_OrderNumber

-- Clustered
CIX_Users_ID
PK_Users  -- Primary key (clustered)

-- Columnstore
CCI_Sales  -- Clustered columnstore
NCCI_Sales -- Nonclustered columnstore
```

---

## 🚨 5-Minute Performance Diagnostic

### Emergency Performance Triage

**Run these queries when database is slow - immediate insights:**

```sql
-- ========================================
-- STEP 1: Currently Running Queries
-- ========================================
SELECT
    r.session_id AS SPID,
    r.status AS Status,
    r.command AS Command,
    DB_NAME(r.database_id) AS DatabaseName,
    r.blocking_session_id AS BlockedBy,
    r.wait_type AS WaitingOn,
    r.wait_time / 1000.0 AS WaitTimeSec,
    r.cpu_time AS CPUTime,
    r.total_elapsed_time / 1000.0 AS ElapsedTimeSec,
    r.reads AS Reads,
    r.writes AS Writes,
    SUBSTRING(
        qt.text,
        (r.statement_start_offset / 2) + 1,
        ((CASE r.statement_end_offset
            WHEN -1 THEN DATALENGTH(qt.text)
            ELSE r.statement_end_offset
        END - r.statement_start_offset) / 2) + 1
    ) AS QueryText
FROM sys.dm_exec_requests r
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) qt
WHERE r.session_id > 50  -- Exclude system processes
ORDER BY r.total_elapsed_time DESC;

-- ========================================
-- STEP 2: Top Wait Types (What's Blocking?)
-- ========================================
SELECT TOP 20
    wait_type AS WaitType,
    waiting_tasks_count AS WaitingTasks,
    wait_time_ms / 1000.0 AS TotalWaitTimeSec,
    (wait_time_ms / 1000.0) / NULLIF(waiting_tasks_count, 0) AS AvgWaitTimeSec,
    CASE
        WHEN wait_type LIKE 'LCK%' THEN '🔒 Locking Issue'
        WHEN wait_type LIKE 'PAGEIOLATCH%' THEN '💾 Disk I/O Issue'
        WHEN wait_type LIKE 'ASYNC_NETWORK_IO%' THEN '🌐 Network Issue'
        WHEN wait_type LIKE 'CXPACKET%' THEN '⚙️ Parallelism Issue'
        WHEN wait_type LIKE 'WRITELOG%' THEN '📝 Log Write Issue'
        ELSE '⚠️ Other'
    END AS Category
FROM sys.dm_os_wait_stats
WHERE wait_type NOT LIKE '%SLEEP%'
    AND wait_type NOT LIKE 'CLR%'
    AND wait_type NOT LIKE 'XE%'
    AND wait_time_ms > 0
ORDER BY wait_time_ms DESC;

-- ========================================
-- STEP 3: Blocking Chains
-- ========================================
WITH BlockingChain AS (
    SELECT
        r.session_id AS BlockedSPID,
        r.blocking_session_id AS BlockingSPID,
        OBJECT_NAME(p.object_id) AS BlockedTable,
        r.wait_time / 1000.0 AS WaitTimeSec,
        SUBSTRING(
            qt.text,
            (r.statement_start_offset / 2) + 1,
            ((CASE r.statement_end_offset
                WHEN -1 THEN DATALENGTH(qt.text)
                ELSE r.statement_end_offset
            END - r.statement_start_offset) / 2) + 1
        ) AS BlockedQuery,
        qt_blocker.text AS BlockingQuery
    FROM sys.dm_exec_requests r
    LEFT JOIN sys.dm_tran_locks p
        ON r.session_id = p.request_session_id
    CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) qt
    LEFT JOIN sys.dm_exec_requests r_blocker
        ON r.blocking_session_id = r_blocker.session_id
    OUTER APPLY sys.dm_exec_sql_text(r_blocker.sql_handle) qt_blocker
    WHERE r.blocking_session_id > 0
)
SELECT * FROM BlockingChain
ORDER BY WaitTimeSec DESC;

-- ========================================
-- STEP 4: Top 20 Most Expensive Queries
-- ========================================
SELECT TOP 20
    qs.execution_count AS ExecutionCount,
    qs.total_elapsed_time / 1000000.0 AS TotalElapsedTimeSec,
    qs.total_elapsed_time / qs.execution_count / 1000.0 AS AvgElapsedTimeMs,
    qs.total_worker_time / 1000000.0 AS TotalCPUTimeSec,
    qs.total_logical_reads AS TotalLogicalReads,
    qs.total_logical_reads / qs.execution_count AS AvgLogicalReads,
    qs.total_physical_reads AS TotalPhysicalReads,
    qs.creation_time AS PlanCreated,
    qs.last_execution_time AS LastExecuted,
    SUBSTRING(
        qt.text,
        (qs.statement_start_offset / 2) + 1,
        ((CASE qs.statement_end_offset
            WHEN -1 THEN DATALENGTH(qt.text)
            ELSE qs.statement_end_offset
        END - qs.statement_start_offset) / 2) + 1
    ) AS QueryText,
    qp.query_plan AS ExecutionPlan
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
CROSS APPLY sys.dm_exec_query_plan(qs.plan_handle) qp
ORDER BY qs.total_elapsed_time DESC;

-- ========================================
-- STEP 5: Missing Indexes (Right Now!)
-- ========================================
SELECT TOP 20
    CONVERT(DECIMAL(18,2),
        migs.avg_user_impact * (migs.user_seeks + migs.user_scans)
    ) AS ImpactScore,
    migs.avg_user_impact AS AvgImpactPercent,
    migs.user_seeks AS Seeks,
    migs.user_scans AS Scans,
    migs.last_user_seek AS LastSeek,
    OBJECT_NAME(mid.object_id) AS TableName,
    mid.equality_columns AS EqualityColumns,
    mid.inequality_columns AS InequalityColumns,
    mid.included_columns AS IncludedColumns,
    'CREATE INDEX IX_' +
        OBJECT_NAME(mid.object_id) + '_' +
        REPLACE(REPLACE(REPLACE(ISNULL(mid.equality_columns, ''), ', ', '_'), '[', ''), ']', '') +
        CASE WHEN mid.inequality_columns IS NOT NULL
            THEN '_' + REPLACE(REPLACE(REPLACE(mid.inequality_columns, ', ', '_'), '[', ''), ']', '')
            ELSE ''
        END +
        ' ON ' + mid.statement +
        ' (' + ISNULL(mid.equality_columns, '') +
        CASE WHEN mid.inequality_columns IS NOT NULL
            THEN CASE WHEN mid.equality_columns IS NOT NULL THEN ', ' ELSE '' END + mid.inequality_columns
            ELSE ''
        END + ')' +
        CASE WHEN mid.included_columns IS NOT NULL
            THEN ' INCLUDE (' + mid.included_columns + ')'
            ELSE ''
        END + ';' AS CreateIndexStatement
FROM sys.dm_db_missing_index_groups mig
INNER JOIN sys.dm_db_missing_index_group_stats migs
    ON migs.group_handle = mig.index_group_handle
INNER JOIN sys.dm_db_missing_index_details mid
    ON mig.index_handle = mid.index_handle
WHERE CONVERT(DECIMAL(18,2),
        migs.avg_user_impact * (migs.user_seeks + migs.user_scans)
    ) > 100
    AND mid.database_id = DB_ID()
ORDER BY ImpactScore DESC;

-- ========================================
-- STEP 6: Buffer Cache Hit Ratio
-- ========================================
SELECT
    (a.cntr_value * 1.0 / b.cntr_value) * 100.0 AS BufferCacheHitRatio,
    CASE
        WHEN (a.cntr_value * 1.0 / b.cntr_value) * 100.0 > 95 THEN '✅ GOOD'
        WHEN (a.cntr_value * 1.0 / b.cntr_value) * 100.0 > 90 THEN '⚠️ OK'
        ELSE '❌ POOR - Need more memory or better indexes'
    END AS Status
FROM
    (SELECT cntr_value FROM sys.dm_os_performance_counters
     WHERE counter_name = 'Buffer cache hit ratio') a
CROSS JOIN
    (SELECT cntr_value FROM sys.dm_os_performance_counters
     WHERE counter_name = 'Buffer cache hit ratio base') b;

-- ========================================
-- STEP 7: High Fragmentation Indexes
-- ========================================
SELECT TOP 20
    OBJECT_NAME(ips.object_id) AS TableName,
    i.name AS IndexName,
    ips.avg_fragmentation_in_percent AS FragmentationPercent,
    ips.page_count AS PageCount,
    ips.page_count * 8 / 1024.0 AS IndexSizeMB,
    CASE
        WHEN ips.avg_fragmentation_in_percent < 10 THEN '✅ No action needed'
        WHEN ips.avg_fragmentation_in_percent < 30 THEN '⚠️ REORGANIZE recommended'
        ELSE '❌ REBUILD required'
    END AS Recommendation,
    'ALTER INDEX ' + i.name + ' ON ' + OBJECT_NAME(ips.object_id) +
    CASE
        WHEN ips.avg_fragmentation_in_percent < 30 THEN ' REORGANIZE;'
        ELSE ' REBUILD;'
    END AS MaintenanceCommand
FROM sys.dm_db_index_physical_stats(
    DB_ID(), NULL, NULL, NULL, 'LIMITED'
) ips
INNER JOIN sys.indexes i
    ON ips.object_id = i.object_id
    AND ips.index_id = i.index_id
WHERE ips.avg_fragmentation_in_percent > 10
    AND ips.page_count > 1000
ORDER BY ips.avg_fragmentation_in_percent DESC;
```

### Quick Actions Based on Results

**If you see high PAGEIOLATCH waits:**
```sql
-- Action: Check for missing indexes and add them
-- Action: Check disk performance
-- Action: Consider SSD upgrade
```

**If you see high LCK_M waits:**
```sql
-- Action: Enable Read Committed Snapshot Isolation
ALTER DATABASE YourDB SET READ_COMMITTED_SNAPSHOT ON;

-- Action: Check for long-running transactions
-- Action: Review query patterns causing locks
```

**If you see blocking:**
```sql
-- Emergency: Kill blocking session (LAST RESORT!)
-- KILL <BlockingSPID>;

-- Better: Identify and optimize the blocking query
-- Better: Enable snapshot isolation
```

**If Buffer Cache Hit Ratio < 90%:**
```sql
-- Action: Add more RAM to server
-- Action: Create missing indexes
-- Action: Optimize queries to read less data
```

---

## 🔎 Identifying Slow Queries

### Understanding Execution Plans

**The most important tool for optimization!**

```sql
-- Enable execution plan and statistics
SET STATISTICS TIME ON;
SET STATISTICS IO ON;

SELECT * FROM Users WHERE Email = 'john@example.com';

-- Output shows:
-- SQL Server parse and compile time:
-- SQL Server Execution Times:
--    CPU time = 15 ms,  elapsed time = 23 ms.
-- Table 'Users'. Scan count 1, logical reads 45234, physical reads 0

-- Look for:
-- ❌ Table Scan (SLOW - reads entire table)
-- ❌ Index Scan (SLOWER - reads entire index)
-- ✅ Index Seek (FAST - direct lookup)
-- ❌ Key Lookup (extra reads - needs covering index)
-- ❌ High logical reads (> 1000 per query)
```

**Visual Execution Plan (SSMS):**
```sql
-- Press Ctrl + M BEFORE running query
-- Or: Query menu → Include Actual Execution Plan

SELECT
    o.OrderID,
    o.OrderDate,
    c.CustomerName,
    SUM(oi.Quantity * oi.Price) AS Total
FROM Orders o
INNER JOIN Customers c ON o.CustomerID = c.CustomerID
INNER JOIN OrderItems oi ON o.OrderID = oi.OrderID
WHERE o.OrderDate >= '2024-01-01'
GROUP BY o.OrderID, o.OrderDate, c.CustomerName;

-- Graphical plan shows:
-- - Operators (Seek, Scan, Join type)
-- - Cost percentages
-- - Row estimates vs. actuals
-- - Warnings (missing indexes, implicit conversions)
```

### Finding Missing Indexes

```sql
-- Comprehensive missing index analysis
SELECT
    CONVERT(DECIMAL(18,2),
        migs.avg_user_impact * (migs.user_seeks + migs.user_scans)
    ) AS ImpactScore,
    migs.avg_user_impact AS AvgImpactPercent,
    migs.user_seeks AS Seeks,
    migs.user_scans AS Scans,
    migs.user_updates AS Updates,
    migs.last_user_seek AS LastSeek,
    migs.last_user_scan AS LastScan,
    OBJECT_NAME(mid.object_id, mid.database_id) AS TableName,
    mid.equality_columns AS EqualityColumns,
    mid.inequality_columns AS InequalityColumns,
    mid.included_columns AS IncludedColumns,
    'CREATE INDEX IX_' +
        OBJECT_NAME(mid.object_id, mid.database_id) + '_' +
        REPLACE(REPLACE(REPLACE(
            ISNULL(mid.equality_columns, ''), ', ', '_'), '[', ''), ']', '') +
        CASE WHEN mid.inequality_columns IS NOT NULL
            THEN '_' + REPLACE(REPLACE(REPLACE(
                mid.inequality_columns, ', ', '_'), '[', ''), ']', '')
            ELSE ''
        END +
        CHAR(13) + 'ON ' + mid.statement +
        CHAR(13) + '(' + ISNULL(mid.equality_columns, '') +
        CASE WHEN mid.inequality_columns IS NOT NULL
            THEN CASE WHEN mid.equality_columns IS NOT NULL
                THEN ', ' ELSE '' END + mid.inequality_columns
            ELSE ''
        END + ')' +
        CASE WHEN mid.included_columns IS NOT NULL
            THEN CHAR(13) + 'INCLUDE (' + mid.included_columns + ')'
            ELSE ''
        END + ';' AS CreateIndexStatement,
    -- Estimated index size
    CONVERT(DECIMAL(18,2),
        (migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) / 100.0
    ) AS EstimatedImprovement
FROM sys.dm_db_missing_index_groups mig
INNER JOIN sys.dm_db_missing_index_group_stats migs
    ON migs.group_handle = mig.index_group_handle
INNER JOIN sys.dm_db_missing_index_details mid
    ON mig.index_handle = mid.index_handle
WHERE mid.database_id = DB_ID()
    AND CONVERT(DECIMAL(18,2),
        migs.avg_user_impact * (migs.user_seeks + migs.user_scans)
    ) > 10
ORDER BY ImpactScore DESC;
```

**⚠️ Missing Index Caveats:**
- DMV suggestions are hints, not commands
- May suggest overlapping/redundant indexes
- May not consider existing indexes
- May suggest too many columns in INCLUDE
- Review and consolidate before creating

### Finding Unused Indexes

```sql
-- Indexes that are NEVER used (candidates for removal)
SELECT
    OBJECT_NAME(i.object_id) AS TableName,
    i.name AS IndexName,
    i.type_desc AS IndexType,
    ISNULL(ius.user_seeks, 0) AS Seeks,
    ISNULL(ius.user_scans, 0) AS Scans,
    ISNULL(ius.user_lookups, 0) AS Lookups,
    ISNULL(ius.user_updates, 0) AS Updates,
    SUM(ps.used_page_count) * 8 / 1024.0 AS IndexSizeMB,
    ius.last_user_seek AS LastSeek,
    ius.last_user_scan AS LastScan,
    CASE
        WHEN ISNULL(ius.user_seeks, 0) + ISNULL(ius.user_scans, 0) +
             ISNULL(ius.user_lookups, 0) = 0
            AND ISNULL(ius.user_updates, 0) > 0
        THEN '❌ NEVER READ, only written - DROP CANDIDATE'
        WHEN ISNULL(ius.user_seeks, 0) + ISNULL(ius.user_scans, 0) +
             ISNULL(ius.user_lookups, 0) < 10
            AND ISNULL(ius.user_updates, 0) > 1000
        THEN '⚠️ Rarely read, frequently written - REVIEW'
        ELSE '✅ Keep'
    END AS Recommendation,
    'DROP INDEX ' + i.name + ' ON ' + OBJECT_NAME(i.object_id) + ';' AS DropCommand
FROM sys.indexes i
LEFT JOIN sys.dm_db_index_usage_stats ius
    ON i.object_id = ius.object_id
    AND i.index_id = ius.index_id
    AND ius.database_id = DB_ID()
LEFT JOIN sys.dm_db_partition_stats ps
    ON i.object_id = ps.object_id
    AND i.index_id = ps.index_id
WHERE OBJECTPROPERTY(i.object_id, 'IsUserTable') = 1
    AND i.type_desc <> 'HEAP'
    AND i.is_primary_key = 0
    AND i.is_unique_constraint = 0
GROUP BY i.object_id, i.name, i.type_desc,
         ius.user_seeks, ius.user_scans, ius.user_lookups,
         ius.user_updates, ius.last_user_seek, ius.last_user_scan
HAVING ISNULL(ius.user_seeks, 0) + ISNULL(ius.user_scans, 0) +
       ISNULL(ius.user_lookups, 0) < 10
ORDER BY Updates DESC, IndexSizeMB DESC;
```

### Top Slowest Queries

```sql
-- Top 50 slowest queries by average execution time
SELECT TOP 50
    qs.execution_count AS Executions,
    qs.total_elapsed_time / 1000000.0 AS TotalElapsedTimeSec,
    qs.total_elapsed_time / qs.execution_count / 1000.0 AS AvgElapsedTimeMs,
    qs.min_elapsed_time / 1000.0 AS MinElapsedTimeMs,
    qs.max_elapsed_time / 1000.0 AS MaxElapsedTimeMs,
    qs.total_worker_time / 1000000.0 AS TotalCPUTimeSec,
    qs.total_worker_time / qs.execution_count / 1000.0 AS AvgCPUTimeMs,
    qs.total_logical_reads AS TotalLogicalReads,
    qs.total_logical_reads / qs.execution_count AS AvgLogicalReads,
    qs.total_physical_reads AS TotalPhysicalReads,
    qs.total_logical_writes AS TotalLogicalWrites,
    qs.creation_time AS PlanCreatedAt,
    qs.last_execution_time AS LastExecutedAt,
    DATEDIFF(MINUTE, qs.creation_time, GETDATE()) AS PlanAgeMinutes,
    DB_NAME(qt.dbid) AS DatabaseName,
    OBJECT_NAME(qt.objectid, qt.dbid) AS ObjectName,
    SUBSTRING(
        qt.text,
        (qs.statement_start_offset / 2) + 1,
        ((CASE qs.statement_end_offset
            WHEN -1 THEN DATALENGTH(qt.text)
            ELSE qs.statement_end_offset
        END - qs.statement_start_offset) / 2) + 1
    ) AS QueryText,
    qp.query_plan AS ExecutionPlan
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
CROSS APPLY sys.dm_exec_query_plan(qs.plan_handle) qp
WHERE qs.execution_count > 1  -- Exclude one-time queries
ORDER BY qs.total_elapsed_time / qs.execution_count DESC;
```

### Query Store (SQL Server 2016+)

**Enable and configure Query Store:**
```sql
-- Enable Query Store
ALTER DATABASE YourDatabase
SET QUERY_STORE = ON (
    OPERATION_MODE = READ_WRITE,
    CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30),
    DATA_FLUSH_INTERVAL_SECONDS = 900,
    INTERVAL_LENGTH_MINUTES = 60,
    MAX_STORAGE_SIZE_MB = 1000,
    QUERY_CAPTURE_MODE = AUTO,
    SIZE_BASED_CLEANUP_MODE = AUTO
);

-- Check Query Store status
SELECT
    actual_state_desc AS Status,
    readonly_reason AS ReadOnlyReason,
    current_storage_size_mb AS CurrentSizeMB,
    max_storage_size_mb AS MaxSizeMB,
    query_capture_mode_desc AS CaptureMode
FROM sys.database_query_store_options;
```

**Find regressed queries (queries that got slower):**
```sql
-- Queries with performance regression
SELECT TOP 20
    q.query_id AS QueryID,
    OBJECT_NAME(q.object_id) AS ObjectName,
    qt.query_sql_text AS QueryText,
    rs_now.avg_duration / 1000.0 AS CurrentAvgDurationMs,
    rs_baseline.avg_duration / 1000.0 AS BaselineAvgDurationMs,
    ((rs_now.avg_duration - rs_baseline.avg_duration) * 100.0) /
        rs_baseline.avg_duration AS RegressionPercent,
    rs_now.avg_logical_io_reads AS CurrentAvgReads,
    rs_baseline.avg_logical_io_reads AS BaselineAvgReads,
    rs_now.count_executions AS RecentExecutions,
    rs_now.last_execution_time AS LastExecuted,
    p_now.plan_id AS CurrentPlanID,
    p_baseline.plan_id AS BaselinePlanID
FROM sys.query_store_query q
JOIN sys.query_store_query_text qt
    ON q.query_text_id = qt.query_text_id
JOIN sys.query_store_plan p_now
    ON q.query_id = p_now.query_id
JOIN sys.query_store_runtime_stats rs_now
    ON p_now.plan_id = rs_now.plan_id
JOIN sys.query_store_runtime_stats_interval rsi_now
    ON rs_now.runtime_stats_interval_id = rsi_now.runtime_stats_interval_id
JOIN sys.query_store_plan p_baseline
    ON q.query_id = p_baseline.query_id
JOIN sys.query_store_runtime_stats rs_baseline
    ON p_baseline.plan_id = rs_baseline.plan_id
JOIN sys.query_store_runtime_stats_interval rsi_baseline
    ON rs_baseline.runtime_stats_interval_id = rsi_baseline.runtime_stats_interval_id
WHERE rsi_now.start_time >= DATEADD(HOUR, -24, GETUTCDATE())  -- Last 24 hours
    AND rsi_baseline.start_time < DATEADD(DAY, -7, GETUTCDATE())  -- Baseline from week ago
    AND rs_now.avg_duration > rs_baseline.avg_duration * 1.5  -- 50% slower
    AND rs_now.count_executions > 10
ORDER BY RegressionPercent DESC;
```

**Force a good query plan:**
```sql
-- Force a specific plan for a query
EXEC sp_query_store_force_plan
    @query_id = 123,
    @plan_id = 456;

-- Unforce plan
EXEC sp_query_store_unforce_plan
    @query_id = 123,
    @plan_id = 456;
```

### Quick Diagnostics Checklist

```sql
-- Run this diagnostic workflow on slow queries:

-- ✅ 1. Get execution plan (Ctrl + M in SSMS)
SET STATISTICS TIME ON;
SET STATISTICS IO ON;
-- Run your slow query here

-- ✅ 2. Check for these red flags in execution plan:
--    ❌ Table Scan / Clustered Index Scan (reading entire table)
--    ❌ High cost operators (> 50% of query cost)
--    ❌ Many rows examined vs returned (e.g., 1M scanned, 10 returned)
--    ❌ Warnings icon (missing indexes, implicit conversions)
--    ❌ Thick arrows (millions of rows between operators)
--    ❌ Key Lookup (need covering index)
--    ✅ Index Seek (good - targeted lookup)

-- ✅ 3. Check if indexes exist
EXEC sp_helpindex 'YourTable';

-- ✅ 4. Check table statistics are updated
DBCC SHOW_STATISTICS ('YourTable', 'IX_YourIndex');

-- If last updated > 1 week ago:
UPDATE STATISTICS YourTable;

-- ✅ 5. Check index fragmentation
SELECT
    i.name,
    ips.avg_fragmentation_in_percent
FROM sys.dm_db_index_physical_stats(
    DB_ID(), OBJECT_ID('YourTable'), NULL, NULL, 'LIMITED'
) ips
JOIN sys.indexes i ON ips.object_id = i.object_id
    AND ips.index_id = i.index_id;

-- ✅ 6. Check for parameter sniffing
-- Look at execution plan for parameter values used
-- Compare with actual parameter values in your query

-- ✅ 7. Check Query Store for plan regression
-- (See Query Store section above)
```

---

## 🎓 Execution Plan Mastery

### 🟡 Intermediate: Understanding Execution Plan Operators

**Common Operators and What They Mean:**

```sql
-- Index Seek ✅ EXCELLENT
-- Fast, targeted lookup using index
-- Cost: Low (0.0033 - 0.1)
-- Example: WHERE ID = 123 with index on ID

-- Index Scan ⚠️ SLOW
-- Reads entire index from start to finish
-- Cost: Medium-High (depends on size)
-- Example: WHERE SomeColumn IS NOT NULL (no index)

-- Table Scan / Clustered Index Scan ❌ VERY SLOW
-- Reads entire table
-- Cost: Very High (10+ for large tables)
-- Fix: Add appropriate index

-- Key Lookup ⚠️ OVERHEAD
-- Additional read to get columns not in index
-- Fix: Use covering index with INCLUDE

-- Nested Loops Join ✅ GOOD for small datasets
-- For each row in outer table, seek in inner table
-- Best when: Outer table small, inner table indexed

-- Hash Match (Join) ⚠️ OK for large datasets
-- Builds hash table in memory
-- Best when: No indexes, large datasets
-- Warning: Can spill to tempdb if not enough memory

-- Merge Join ✅ BEST for sorted data
-- Both inputs sorted, merge together
-- Requires: Both inputs sorted on join key

-- Sort ⚠️ EXPENSIVE
-- Sorts data in memory or tempdb
-- Fix: Add index on ORDER BY column
```

### Reading Execution Plans

```sql
-- Enable actual execution plan
SET STATISTICS TIME ON;
SET STATISTICS IO ON;

-- Example query
SELECT c.CustomerName, COUNT(o.OrderID) AS OrderCount
FROM Customers c
LEFT JOIN Orders o ON c.CustomerID = o.CustomerID
WHERE c.City = 'Seattle'
GROUP BY c.CustomerName
ORDER BY OrderCount DESC;

-- What to look for:
-- 1. Thick arrows = Many rows = Potential problem
-- 2. High cost % operators = Optimization target
-- 3. Warnings (yellow triangle) = Missing stats, implicit conversion
-- 4. Actual vs Estimated rows = Statistics issue if way off
```

---

## ⚡ Query Optimization Techniques

### 🟢 Beginner: Essential Optimizations

```sql
-- 1. SELECT only what you need
-- ❌ BAD
SELECT * FROM Users;

-- ✅ GOOD
SELECT FirstName, LastName, Email FROM Users;

-- 2. Use sargable WHERE clauses
-- ❌ BAD (function on column)
WHERE YEAR(OrderDate) = 2024

-- ✅ GOOD (sargable)
WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01'

-- 3. Use EXISTS instead of IN
-- ❌ SLOWER
WHERE CustomerID IN (SELECT CustomerID FROM Orders)

-- ✅ FASTER
WHERE EXISTS (SELECT 1 FROM Orders o WHERE o.CustomerID = c.CustomerID)

-- 4. Avoid leading wildcards
-- ❌ BAD
WHERE Email LIKE '%@gmail.com'

-- ✅ GOOD
WHERE Email LIKE 'john%'

-- 5. Batch operations
-- ❌ SLOW
WHILE ... INSERT INTO ... (one at a time)

-- ✅ FAST
INSERT INTO ... SELECT ... FROM ... (batch)
```

---

## 🔒 Transaction Management & Locking

### 🟡 Intermediate: Isolation Levels

```sql
-- READ UNCOMMITTED (dirty reads possible)
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
-- ⚠️ Can read uncommitted data
-- Use case: Reports where accuracy isn't critical

-- READ COMMITTED (default, prevents dirty reads)
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
-- ✅ Most common
-- Can still have non-repeatable reads

-- READ COMMITTED SNAPSHOT ISOLATION (RCSI) ⭐ RECOMMENDED
ALTER DATABASE YourDB SET READ_COMMITTED_SNAPSHOT ON;
-- ✅ No blocking on reads
-- ✅ Readers don't block writers
-- ⚠️ Uses tempdb for row versioning

-- REPEATABLE READ (prevents non-repeatable reads)
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
-- ⚠️ Locks held until transaction ends
-- Can cause blocking

-- SNAPSHOT (full isolation using versioning)
SET TRANSACTION ISOLATION LEVEL SNAPSHOT;
-- ✅ Full consistency without blocking
-- ⚠️ Uses tempdb

-- SERIALIZABLE (highest isolation)
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
-- ❌ Slowest, most locking
-- Rarely needed
```

### Deadlock Prevention

```sql
-- RULE 1: Access tables in same order
-- ❌ BAD (Transaction A and B access in different order)
-- Transaction A: UPDATE Customers...; UPDATE Orders...;
-- Transaction B: UPDATE Orders...; UPDATE Customers...;  -- DEADLOCK!

-- ✅ GOOD (Both access in same order)
-- Transaction A: UPDATE Customers...; UPDATE Orders...;
-- Transaction B: UPDATE Customers...; UPDATE Orders...;  -- No deadlock

-- RULE 2: Keep transactions short
BEGIN TRANSACTION
    UPDATE Customers SET LastName = 'Smith' WHERE ID = 1;
COMMIT;  -- Commit quickly

-- RULE 3: Use appropriate isolation level
-- Use RCSI to reduce locking

-- RULE 4: Use NOLOCK hint carefully (dirty reads)
SELECT * FROM Orders WITH (NOLOCK);  -- Reads uncommitted data
```

---

## 💾 Query Plan Cache Management

### 🟡 Intermediate: Parameter Sniffing

```sql
-- THE PROBLEM:
CREATE PROCEDURE GetOrders @Status VARCHAR(20)
AS
    SELECT * FROM Orders WHERE Status = @Status;

-- First call: @Status = 'Pending' (1% of data)
-- SQL Server creates plan with Index Seek
-- Plan cached

-- Second call: @Status = 'Completed' (99% of data)
-- Still uses Index Seek plan (BAD! Should use Scan)

-- SOLUTION 1: Local variable (recompile each time)
CREATE PROCEDURE GetOrders @Status VARCHAR(20)
AS
    DECLARE @LocalStatus VARCHAR(20) = @Status;
    SELECT * FROM Orders WHERE Status = @LocalStatus;

-- SOLUTION 2: RECOMPILE hint
CREATE PROCEDURE GetOrders @Status VARCHAR(20)
AS
    SELECT * FROM Orders WHERE Status = @Status
    OPTION (RECOMPILE);

-- SOLUTION 3: OPTIMIZE FOR hint
CREATE PROCEDURE GetOrders @Status VARCHAR(20)
AS
    SELECT * FROM Orders WHERE Status = @Status
    OPTION (OPTIMIZE FOR (@Status = 'Completed'));

-- Clear specific plan from cache
DBCC FREEPROCCACHE;  -- ⚠️ Clears ALL plans
```

---

## 📐 Index Design Methodology

### 🟡 Intermediate: Systematic Index Design

**Step 1: Analyze Query Patterns**
```sql
-- Find most frequently executed queries
SELECT TOP 20
    qs.execution_count,
    SUBSTRING(qt.text, 1, 100) AS QuerySnippet
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
ORDER BY qs.execution_count DESC;
```

**Step 2: Calculate Selectivity**
```sql
-- Selectivity = Unique Values / Total Rows
SELECT
    'Email' AS ColumnName,
    COUNT(DISTINCT Email) * 1.0 / COUNT(*) AS Selectivity
FROM Users;

-- Selectivity Guide:
-- > 95% = EXCELLENT for indexing
-- 50-95% = GOOD for indexing
-- < 50% = Consider filtered index
-- < 10% = Probably not worth indexing
```

**Step 3: Design Index**
```
1. Equality columns first (WHERE Status = 'Pending')
2. Range columns next (WHERE OrderDate > '2024-01-01')
3. ORDER BY columns last
4. INCLUDE columns used in SELECT but not in WHERE/JOIN/ORDER BY
```

---

## 🚀 Advanced Optimization Methods

### 🔴 Advanced: Table Partitioning

```sql
-- Partition large table by date
-- Step 1: Create partition function
CREATE PARTITION FUNCTION PF_OrderDate (DATE)
AS RANGE RIGHT FOR VALUES
    ('2023-01-01', '2024-01-01', '2025-01-01');

-- Step 2: Create partition scheme
CREATE PARTITION SCHEME PS_OrderDate
AS PARTITION PF_OrderDate ALL TO ([PRIMARY]);

-- Step 3: Create partitioned table
CREATE TABLE Orders (
    OrderID INT,
    OrderDate DATE,
    CustomerID INT,
    Amount DECIMAL(10,2)
) ON PS_OrderDate(OrderDate);

-- Benefit: Query only scans relevant partition
SELECT * FROM Orders
WHERE OrderDate >= '2024-01-01' AND OrderDate < '2025-01-01';
-- Only scans 2024 partition!
```

### 🔴 Advanced: In-Memory OLTP

```sql
-- Create memory-optimized table (10-100x faster)
-- Step 1: Add memory-optimized filegroup
ALTER DATABASE YourDB ADD FILEGROUP InMemoryFG
CONTAINS MEMORY_OPTIMIZED_DATA;

ALTER DATABASE YourDB ADD FILE (
    NAME = 'InMemoryData',
    FILENAME = 'C:\Data\InMemoryData'
) TO FILEGROUP InMemoryFG;

-- Step 2: Create memory-optimized table
CREATE TABLE SessionCache (
    SessionID INT PRIMARY KEY NONCLUSTERED HASH WITH (BUCKET_COUNT = 1000000),
    UserID INT NOT NULL,
    Data NVARCHAR(MAX),
    LastActivity DATETIME2
) WITH (MEMORY_OPTIMIZED = ON, DURABILITY = SCHEMA_AND_DATA);
```

---

## ☁️ Azure SQL Optimizations

### Azure SQL Database Specific

```sql
-- 1. Use Query Performance Insight
-- View in Azure Portal → SQL Database → Intelligent Performance

-- 2. Enable Automatic Tuning
ALTER DATABASE CURRENT
SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON);

-- 3. Use DTU/vCore recommendations
-- Azure Portal shows DTU usage and recommendations

-- 4. Configure Read Scale-Out
-- Connection string: ApplicationIntent=ReadOnly
-- Reads go to secondary replica

-- 5. Use Elastic Pools for multiple databases
-- Share resources across databases

-- 6. Enable Geo-Replication for DR
-- Azure Portal → Geo-Replication → Add secondary
```

---

## 🔧 Troubleshooting Playbooks

### PLAYBOOK 1: Query Suddenly Slow

```sql
-- Step 1: Check if plan changed
SELECT
    qs.creation_time,
    qs.execution_count,
    qs.total_elapsed_time / qs.execution_count / 1000 AS AvgMs,
    SUBSTRING(qt.text, 1, 200) AS QueryText
FROM sys.dm_exec_query_stats qs
CROSS APPLY sys.dm_exec_sql_text(qs.sql_handle) qt
WHERE qt.text LIKE '%YourQuery%'
ORDER BY qs.creation_time DESC;

-- Step 2: Check statistics freshness
DBCC SHOW_STATISTICS('YourTable', 'IX_YourIndex');
-- If updated > 1 week ago:
UPDATE STATISTICS YourTable;

-- Step 3: Check index fragmentation
-- (See Fragmentation section above)

-- Step 4: Clear plan cache for this query
DBCC FREEPROCCACHE;
```

### PLAYBOOK 2: Database Unresponsive

```sql
-- Step 1: Check for blocking
SELECT *
FROM sys.dm_exec_requests
WHERE blocking_session_id > 0;

-- Step 2: Check wait types
SELECT TOP 10 *
FROM sys.dm_os_wait_stats
WHERE wait_type NOT LIKE '%SLEEP%'
ORDER BY wait_time_ms DESC;

-- Step 3: Kill blocking session (LAST RESORT)
KILL <session_id>;

-- Step 4: Enable RCSI to reduce blocking
ALTER DATABASE YourDB SET READ_COMMITTED_SNAPSHOT ON;
```

---

## 🌍 Real-World Scenarios

### Scenario: E-Commerce Black Friday

**Problem**: 50x traffic, database melting down

**Solution**:
```sql
-- 1. Add missing indexes immediately
-- (Run missing index query from diagnostic section)

-- 2. Enable result set caching in application
-- Cache frequently accessed product data for 5 minutes

-- 3. Use read replicas
-- Route all product browsing to read replica
-- Only writes go to primary

-- 4. Temporarily disable non-critical jobs
-- Pause reporting queries during peak

-- 5. Increase DTU/vCore temporarily
-- Scale up in Azure Portal
```

### Scenario: Multi-Tenant SaaS Performance

**Problem**: One large tenant slowing everyone down

**Solution**:
```sql
-- 1. Create filtered indexes per tenant
CREATE INDEX IX_Orders_Tenant1
ON Orders(OrderDate, CustomerID)
WHERE TenantID = 1;

-- 2. Use Resource Governor (Enterprise Edition)
CREATE RESOURCE POOL TenantPool
WITH (MAX_CPU_PERCENT = 30);

-- 3. Partition by TenantID
CREATE PARTITION FUNCTION PF_Tenant (INT)
AS RANGE RIGHT FOR VALUES (1, 2, 3, ...);

-- 4. Consider separate database for large tenant
```

---

## 🚨 Error Message Decoder

### "Timeout expired. The timeout period elapsed..."

**Causes**:
1. Missing index (80% of cases)
2. Blocking/deadlocks (10%)
3. Resource contention (10%)

**Solutions**:
```sql
-- Check missing indexes
-- (See missing index query above)

-- Check blocking
SELECT * FROM sys.dm_exec_requests WHERE blocking_session_id > 0;

-- Enable RCSI
ALTER DATABASE YourDB SET READ_COMMITTED_SNAPSHOT ON;
```

### "Transaction deadlocked..."

**Solution**:
```sql
-- Review deadlock graph
-- SSMS → Management → SQL Server Logs → Search "deadlock"

-- Ensure transactions access tables in same order
-- Keep transactions short
-- Use RCSI
ALTER DATABASE YourDB SET READ_COMMITTED_SNAPSHOT ON;
```

---

## 🚀 Quick Wins Checklist

**First 30 Minutes - Immediate Impact:**

- [ ] **Find top 5 missing indexes** - Run missing index DMV query
- [ ] **Update all statistics** - `EXEC sp_updatestats;`
- [ ] **Rebuild fragmented indexes** - Fragmentation > 30%
- [ ] **Enable RCSI** - `ALTER DATABASE YourDB SET READ_COMMITTED_SNAPSHOT ON;`
- [ ] **Enable Query Store** - Track query performance automatically
- [ ] **Add covering indexes** - For top 10 most frequent queries
- [ ] **Fix implicit conversions** - Check execution plans for CONVERT_IMPLICIT
- [ ] **Remove unused indexes** - Drop indexes never used
- [ ] **Enable auto-update statistics** - Should be ON by default
- [ ] **Connection pooling enabled** - Check application configuration

**Expected Impact**: 50-300% performance improvement in 30 minutes!

---

## ✅ Best Practices

### Index Design
- ✅ Primary key on every table
- ✅ Foreign keys indexed
- ✅ Covering indexes for critical queries
- ✅ Filtered indexes for partial data
- ❌ No more than 5-7 non-clustered indexes per table
- ❌ No indexes on columns with low selectivity

### Query Writing
- ✅ SELECT only needed columns
- ✅ Use sargable WHERE clauses
- ✅ EXISTS instead of IN
- ✅ Proper data types
- ❌ No SELECT *
- ❌ No functions on indexed columns in WHERE

### Maintenance
- ✅ Update statistics weekly
- ✅ Rebuild fragmented indexes monthly
- ✅ Monitor slow query log daily
- ✅ Review execution plans regularly
- ✅ Test in production-like environment

### Production
- ✅ Enable Query Store
- ✅ Enable RCSI for OLTP workloads
- ✅ Connection pooling
- ✅ Application-level caching
- ✅ Read replicas for read-heavy workloads

---

## 📊 Performance Metrics

### Performance Budget

| Query Type | Target | Acceptable | Poor |
|------------|--------|------------|------|
| PK lookup | <5ms | <20ms | >50ms |
| Simple WHERE | <10ms | <50ms | >100ms |
| JOIN 2-3 tables | <50ms | <200ms | >500ms |
| Aggregation | <100ms | <1s | >5s |
| Report query | <5s | <30s | >60s |

### Monitoring Queries

```sql
-- Buffer cache hit ratio (should be > 95%)
SELECT
    (a.cntr_value * 1.0 / b.cntr_value) * 100.0 AS BufferCacheHitRatio
FROM
    (SELECT cntr_value FROM sys.dm_os_performance_counters
     WHERE counter_name = 'Buffer cache hit ratio') a
CROSS JOIN
    (SELECT cntr_value FROM sys.dm_os_performance_counters
     WHERE counter_name = 'Buffer cache hit ratio base') b;

-- Page life expectancy (should be > 300 seconds)
SELECT
    cntr_value AS PageLifeExpectancySeconds
FROM sys.dm_os_performance_counters
WHERE counter_name = 'Page life expectancy';

-- Batch requests per second
SELECT
    cntr_value AS BatchRequestsPerSec
FROM sys.dm_os_performance_counters
WHERE counter_name = 'Batch Requests/sec';
```

---

## 🎯 Key Takeaways

1. **Indexes are essential** - But balance read vs. write performance
2. **Execution plans tell the truth** - Always check them for slow queries
3. **Covering indexes** are often the fastest solution
4. **Avoid functions** on indexed columns in WHERE clauses
5. **Enable RCSI** - Reduces blocking in OLTP workloads
6. **Query Store** - Track performance automatically (SQL Server 2016+)
7. **Update statistics** regularly - Critical for good execution plans
8. **Monitor and measure** - You can't optimize what you don't measure
9. **Test in production-like environment** - Performance differs with data size
10. **Start with quick wins** - Missing indexes, statistics, RCSI

---

## 📚 Additional Resources

### SQL Server Documentation
- [Index Architecture](https://docs.microsoft.com/sql/relational-databases/sql-server-index-design-guide)
- [Query Performance Tuning](https://docs.microsoft.com/sql/relational-databases/performance/query-performance-tuning)
- [Execution Plans](https://docs.microsoft.com/sql/relational-databases/performance/execution-plans)

### Tools
- **SQL Server Management Studio (SSMS)** - Execution plans, statistics
- **Azure Data Studio** - Cross-platform query tool
- **SQL Server Profiler** - Trace query execution
- **Extended Events** - Lightweight profiling
- **sp_Blitz** - Free health check scripts (Brent Ozar)

### Books
- "SQL Performance Explained" by Markus Winand
- "SQL Server Execution Plans" by Grant Fritchey
- "SQL Server 2019 Administration Inside Out" by Randolph West

---

**Remember**: Premature optimization is the root of all evil. Profile first, optimize second!

**The most important query**: What is causing slowness RIGHT NOW?
- Run the 5-Minute Diagnostic
- Check execution plans
- Add missing indexes
- Monitor and iterate

Happy optimizing! 🚀

---

*This guide is specific to Microsoft SQL Server. For other databases, consult their respective documentation.*