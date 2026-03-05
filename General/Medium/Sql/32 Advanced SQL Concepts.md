
Advanced SQL concepts

### 1. Correlated Subqueries

A correlated subquery, however, depends on the outer query. It’s a  
subquery that uses values from the outer query

```sql
SELECT column_name(s) FROM table_name1 outer   
WHERE column_name operator  
(SELECT column_name(s) FROM table_name2 inner   
WHERE outer.column_name =  
inner.column_name);
```

### 2. EXISTS

The EXISTS operator is used to test for the existence of any record in  
a subquery.

```sql
SELECT column_name(s) FROM table_name1   
WHERE EXISTS   
(SELECT column_name FROM table_name2 WHERE condition)
```

### 3. Common Table Expressions (CTEs)

A CTE provides the significant advantage of being able to reference  
itself, thereby creating a recursive CTE

```sql
WITH RECURSIVE cte_name (column_name(s)) AS (  
SQL query  
)  
SELECT * FROM cte_name;
```

### 4.PIVOT

Pivoting data can be achieved in SQL by using aggregate functions in  
concert with a CASE statement in the query.

### 5. UNPIVOT

Similarly, Unpivoting data can be achieved in SQL by using aggregate functions in concert with a CASE statement in the query

### 6. Window Functions

Window functions provide the ability to perform calculations across  
sets of rows that are related to the current query row

### 7. RANK()

The RANK() function is a window function that assigns a unique rank to  
each row within the partition of a result se

```sql
SELECT column_name(s), RANK() OVER (ORDER BY column_name) FROM table_name;
```

### 8. DENSE_RANK()

This function provides the same functionality as RANK(), but in the  
event of a tie, it doesn’t skip any ranks

### 9. ROW_NUMBER()

This function assigns a unique row number for each row, but makes no  
promise about what order that will be in, or even that the order will  
be deterministic.

```sql
SELECT column_name(s),   
ROW_NUMBER() OVER (ORDER BY column_name) FROM  
table_name
```

### 10. NTILE()

This function distributes the rows in an ordered partition into a  
specified number of groups, or buckets, and assigns a unique bucket  
number to each row in the partition.

```sql
SELECT column_name(s),   
NTILE(bucket_number) OVER (ORDER BY column_name) FROM  
table_name
```

### 11. LAG() and LEAD()

These functions fetch the value of a given expression for the previous  
row (LAG) or the next row (LEAD) in the same result set without the use  
of a self-join.
```sql
SELECT column_name,   
LAG(column_name) OVER (ORDER BY column_name),  
LEAD(column_name) OVER (ORDER BY column_name)   
FROM table_name;
```

### 12. FIRST_VALUE() and LAST_VALUE()

These functions return the first or the last value from an ordered set  
of values in SQL
```sql
SELECT column_name,   
FIRST_VALUE(column_name) OVER (ORDER BY column_name),  
LAST_VALUE(column_name) OVER (ORDER BY column_name)   
FROM table_name;
```

### 13. CUME_DIST()

This function computes the cumulative distribution of a value in a  
group of values in SQL. That is, CUME_DIST computes the relative  
position of a specified value in a group of values.

```sql
SELECT column_name, CUME_DIST() OVER (ORDER BY column_name) FROM table_name;
```

### 14. PERCENT_RANK()

This function computes the relative rank of a row returned by a query  
in SQL
```sql
SELECT column_name,   
PERCENT_RANK() OVER (ORDER BY column_name)   
FROM table_name;
```

### 15. Database Administration

This involves a wide array of operations, from managing users and  
permissions, to performance optimization, backups, and migrating data  
between systems.

### 16. Materialized Views

These are similar to regular views, but the results are stored in a  
physical table for performance gains.

### 17. Analytic Functions

These are a type of function that compute across a set of table rows  
that are somehow related to the current row.

### 18. Sequences

Sequences are database objects from which multiple users may generate  
unique integers.

### 19. Synonyms

A synonym is an alias for a database object, providing a layer of abstraction  
that can simplify SQL statements for database users

### 20. Partitioning

This is a technique to divide a large database table into smaller, more  
manageable parts without having to create separate tables for each part.

### 21. User-Defined Functions (UDFs)

UDFs are functions defined by the user at the database level
```sql
CREATE FUNCTION function_name (@param1 int, @param2 nvarchar(50))  
RETURNS TABLE  
AS  
RETURN  
(  
SELECT column1, column2  
FROM table_name  
WHERE column1 = @param1 AND column2 = @param2  
);
```

### 22. Dynamic SQL

Dynamic SQL allows programmers to write SQL statements that will be  
executed at runtime
```sql
DECLARE @column_name VARCHAR(100);  
SET @column_name = 'column1';  
EXEC('SELECT ' + @column_name + ' FROM table_name');
```

### 23. Recursive Queries

Recursive queries are used to query hierarchical data.
```sql
WITH RECURSIVE recursive_query AS (  
  SELECT column1, column2  
  FROM table_name  
  WHERE condition1  
  UNION ALL  
  SELECT r.column1, r.column2  
  FROM table_name AS r  
  JOIN recursive_query AS rq ON r.column3 = rq.column1  
)  
SELECT * FROM recursive_query
```

### 24. Database Replication

Replication is a set of technologies for copying and distributing  
data and database objects from one database to another.

### 25. Database Sharding

Sharding is a type of database partitioning that separates large  
databases into smaller, faster, more easily managed parts.

### 26. Database Migration

The process of moving your data from one database engine to another.

### 27. Database Performance Tuning

A wide variety of practices used to make a database run faster.

### 28. Distributed Databases

A distributed database is a database that consists of two or more  
files located in different sites either on the same network or on  
entirely different networks.

### 29. Database Security

Practices used to protect your database from intentional or  
accidental threats, risks, or attacks

### 30. Database Backup and Restoration

Essential operations for preserving and recovering data.

### 31. Cloud Databases

Modern databases hosted on the cloud, providing benefits such as  
scalability and flexibility.

### 32. Data Lake and Data Lakehouse

A data lake is a storage repository that holds a vast amount of raw  
data. A data lakehouse blends the best elements of a data warehouse  
and a data lake.