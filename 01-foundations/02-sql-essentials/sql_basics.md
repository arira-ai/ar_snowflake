`ar_snowflake/01-foundations/02-sql-essentials/snowflake-sql-essentials.md`

# Snowflake SQL Essentials

This section covers the SQL capabilities of Snowflake. Snowflake supports ANSI SQL while also introducing several extensions optimized for cloud-native analytics, semi-structured data, and scalable data processing.

Understanding these SQL fundamentals is necessary before working with ingestion pipelines, analytics workloads, and data engineering tasks.

---

# SQL in Snowflake

Snowflake SQL is built on **ANSI SQL standards** with additional capabilities designed for modern data platforms.

Snowflake SQL supports:

* Data definition
* Data manipulation
* Data querying
* Data control and security
* Semi-structured data processing

Major SQL categories include:

* DDL (Data Definition Language)
* DML (Data Manipulation Language)
* DQL (Data Query Language)
* TCL (Transaction Control Language)
* DCL (Data Control Language)

---

# Snowflake SQL Architecture Context

```
flowchart LR
A[User SQL Query]
B[Cloud Services Parser]
C[Query Optimizer]
D[Virtual Warehouse Execution]
E[Storage Layer]

A --> B
B --> C
C --> D
D --> E
```

SQL queries are processed by the cloud services layer and executed by virtual warehouses.

---

# Data Definition Language (DDL)

DDL statements define database objects such as databases, schemas, tables, and views.

Common DDL operations:

Create database

```sql
CREATE DATABASE sales_db;
```

Create schema

```sql
CREATE SCHEMA sales_db.raw;
```

Create table

```sql
CREATE TABLE customers (
  customer_id INTEGER,
  name STRING,
  email STRING,
  created_at TIMESTAMP
);
```

Modify table structure

```sql
ALTER TABLE customers ADD COLUMN phone STRING;
```

Remove objects

```sql
DROP TABLE customers;
```

DDL statements modify metadata stored in the cloud services layer.

---

# Data Manipulation Language (DML)

DML operations modify the data stored in tables.

Insert records

```sql
INSERT INTO customers VALUES (1,'John','john@email.com',CURRENT_TIMESTAMP);
```

Update records

```sql
UPDATE customers
SET email='new@email.com'
WHERE customer_id=1;
```

Delete records

```sql
DELETE FROM customers
WHERE customer_id=1;
```

Snowflake internally handles updates and deletes using immutable micro-partitions.

---

# Data Query Language (DQL)

DQL retrieves data from tables.

Basic query

```sql
SELECT * FROM customers;
```

Filtering

```sql
SELECT name,email
FROM customers
WHERE created_at > '2025-01-01';
```

Aggregation

```sql
SELECT COUNT(*) FROM customers;
```

Grouping

```sql
SELECT country, COUNT(*)
FROM customers
GROUP BY country;
```

Joins

```sql
SELECT o.order_id, c.name
FROM orders o
JOIN customers c
ON o.customer_id = c.customer_id;
```

---

# Snowflake-Specific SQL Extensions

Snowflake includes several SQL capabilities that are uncommon in traditional databases.

Major extensions include:

* Time Travel queries
* Semi-structured data queries
* Cloning
* Streams and tasks
* Result caching
* Variant data type

---

# Querying Historical Data (Time Travel)

Snowflake allows querying past versions of data.

```sql
SELECT *
FROM orders
AT (TIMESTAMP => '2025-01-01 10:00:00');
```

Another option:

```sql
SELECT *
FROM orders
BEFORE (STATEMENT => 'query_id');
```

This feature enables historical analytics and recovery.

---

# Semi-Structured Data Support

Snowflake can store and query JSON, Avro, Parquet, and XML data.

The `VARIANT` data type stores semi-structured data.

Example table

```sql
CREATE TABLE events (
  id INTEGER,
  event_data VARIANT
);
```

Insert JSON data

```sql
INSERT INTO events
SELECT 1, PARSE_JSON('{"user":"john","action":"login"}');
```

Query JSON fields

```sql
SELECT event_data:user
FROM events;
```

Snowflake automatically parses JSON data during query execution.

---

# Common Table Expressions (CTE)

CTEs allow modular SQL queries.

Example

```sql
WITH sales_summary AS (
  SELECT customer_id, SUM(amount) total_sales
  FROM orders
  GROUP BY customer_id
)
SELECT *
FROM sales_summary
WHERE total_sales > 1000;
```

CTEs improve readability and maintainability of complex queries.

---

# Window Functions

Window functions perform calculations across a set of rows.

Example

```sql
SELECT
  customer_id,
  amount,
  SUM(amount) OVER (PARTITION BY customer_id) total_customer_sales
FROM orders;
```

Use cases:

* Ranking
* Running totals
* Moving averages

---

# Query Result Caching

Snowflake automatically caches query results.

```
flowchart LR
A[Query Submitted]
B[Check Result Cache]
C[Return Cached Result]
D[Execute Query]

A --> B
B --> C
B --> D
```

If the same query runs again and underlying data is unchanged, the cached result is returned instantly.

---

# Query Performance Concepts

Several features improve query performance.

Partition pruning
Columnar storage scanning
Parallel execution
Query optimization
Result caching

These mechanisms operate automatically without manual tuning.

---

# Transaction Control

Snowflake supports standard transaction management.

Start transaction

```sql
BEGIN;
```

Commit changes

```sql
COMMIT;
```

Rollback changes

```sql
ROLLBACK;
```

Transactions ensure data consistency and integrity.

---

# Role-Based Access SQL

Snowflake uses SQL commands for access control.

Create role

```sql
CREATE ROLE analyst;
```

Grant role to user

```sql
GRANT ROLE analyst TO USER john;
```

Grant permissions

```sql
GRANT SELECT ON TABLE customers TO ROLE analyst;
```

Security policies are enforced through roles.

---

# Key Snowflake SQL Characteristics

ANSI SQL compatibility
Automatic query optimization
Native support for semi-structured data
Built-in historical querying
Fully managed performance tuning

These capabilities make Snowflake SQL suitable for large-scale analytics and modern data engineering workloads.
