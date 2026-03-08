### Auditing and Monitoring in Snowflake Inc.

Auditing and monitoring record **who accessed data, what operations occurred, and when they occurred**.
These capabilities support **security investigations, regulatory compliance, governance controls, and operational monitoring**.

Snowflake exposes auditing information through **system views, metadata tables, and query logs**.

Primary audit sources:

* Query History
* Access History
* Login History
* Account Usage Views
* Information Schema Views

---

# 1. Query History

Query History records **every SQL statement executed in Snowflake**.

Captured information includes:

* User executing the query
* Role used
* SQL text
* Execution start and end time
* Warehouse used
* Query status
* Rows scanned or returned

Purpose:

* Performance monitoring
* Security investigation
* Operational debugging
* Data usage analysis

Example query:

```sql
SELECT
    query_id,
    user_name,
    role_name,
    query_text,
    start_time,
    execution_status
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
ORDER BY start_time DESC
LIMIT 50;
```

Security teams analyze this data to detect:

* Suspicious data extraction
* Unauthorized schema access
* Large data scans
* Long-running queries

---

# 2. Access History

Access History tracks **which tables, columns, or views were accessed by queries**.

Unlike Query History, which logs SQL statements, Access History records **actual data objects touched during execution**.

Information recorded:

* Query ID
* User
* Objects accessed
* Columns referenced
* Base tables behind views
* Policies applied

Example query:

```sql
SELECT
    query_id,
    user_name,
    objects_accessed,
    columns_accessed
FROM SNOWFLAKE.ACCOUNT_USAGE.ACCESS_HISTORY
ORDER BY query_start_time DESC;
```

Governance use cases:

* Identify who accessed sensitive data
* Trace lineage through views
* Audit column-level data usage
* Investigate data leakage

Access History enables **fine-grained data auditing**.

---

# 3. Login History

Login History records **authentication attempts to the Snowflake account**.

Captured information:

* Username
* Login timestamp
* Source IP address
* Authentication method
* Login status (success or failure)
* Client application

Example:

```sql
SELECT
    user_name,
    event_timestamp,
    client_ip,
    is_success,
    reported_client_type
FROM SNOWFLAKE.ACCOUNT_USAGE.LOGIN_HISTORY
ORDER BY event_timestamp DESC;
```

Security teams monitor:

* Failed login attempts
* Suspicious geographic locations
* Unauthorized network access
* Brute-force login patterns

Login History integrates with **network policies and identity systems**.

---

# 4. Account Usage Views

Snowflake provides a special schema called:

```
SNOWFLAKE.ACCOUNT_USAGE
```

This schema contains **system views that track activity across the entire account**.

Key views include:

| View                   | Purpose                     |
| ---------------------- | --------------------------- |
| QUERY_HISTORY          | Track executed queries      |
| ACCESS_HISTORY         | Track data objects accessed |
| LOGIN_HISTORY          | Track login activity        |
| WAREHOUSE_LOAD_HISTORY | Warehouse performance       |
| DATABASES              | Database metadata           |
| USERS                  | User definitions            |
| ROLES                  | Role assignments            |
| GRANTS_TO_ROLES        | Permission assignments      |

Example:

```sql
SELECT *
FROM SNOWFLAKE.ACCOUNT_USAGE.USERS;
```

Account Usage views enable **enterprise-wide governance monitoring**.

---

# 5. Information Schema Views

Another metadata layer exists inside each database.

```
INFORMATION_SCHEMA
```

These views provide **near-real-time metadata** about objects and activity.

Examples:

* TABLES
* VIEWS
* COLUMNS
* QUERY_HISTORY
* STAGES
* FILE_FORMATS

Example:

```sql
SELECT *
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = 'FINANCE';
```

Difference:

| Source             | Scope                    |
| ------------------ | ------------------------ |
| ACCOUNT_USAGE      | Entire Snowflake account |
| INFORMATION_SCHEMA | Current database         |

---

# 6. Data Access Tracking for Compliance

Organizations must demonstrate **data accountability** for regulations such as:

* GDPR
* HIPAA
* SOC2
* PCI-DSS

Snowflake auditing allows organizations to answer:

```
Who accessed the data?
When did access occur?
What data objects were accessed?
Which role was used?
From which network did the user connect?
```

Audit pipeline example:

```
Snowflake Logs
     ↓
ACCOUNT_USAGE Views
     ↓
Security Monitoring Tool
     ↓
Compliance Reporting
```

Logs are commonly exported to:

* SIEM systems
* Security dashboards
* Compliance reporting tools

---

# 7. Example: Investigating Sensitive Data Access

Scenario:

Sensitive table

```
FINANCE.SALARIES
```

Security team runs audit query.

```sql
SELECT
    user_name,
    query_start_time,
    objects_accessed
FROM SNOWFLAKE.ACCOUNT_USAGE.ACCESS_HISTORY
WHERE objects_accessed LIKE '%SALARIES%';
```

Result shows:

* Which user accessed salary data
* When access occurred
* Through which query

This enables **complete audit traceability**.

---

# 8. Monitoring Role Activity

Example: determine role activity.

```sql
SELECT
    role_name,
    COUNT(*) AS query_count
FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
GROUP BY role_name
ORDER BY query_count DESC;
```

This identifies:

* Most used roles
* Privilege misuse
* Unexpected role activity

---

# 9. Security Monitoring Architecture

Typical enterprise monitoring architecture:

```
Users
   ↓
Snowflake Platform
   ↓
Audit Logs Generated
   ↓
ACCOUNT_USAGE Views
   ↓
Export to Security Tools
   ↓
SIEM / Monitoring Dashboards
```

Monitoring systems generate alerts for:

* Large data exports
* Repeated failed logins
* Unusual query activity
* Unauthorized data access

---

# 10. Governance Benefits

Snowflake auditing provides:

* Full query traceability
* Column-level data visibility
* Authentication monitoring
* Permission auditing
* Regulatory compliance support

These capabilities allow organizations to implement **enterprise-grade data governance and security monitoring** across the entire Snowflake environment.
