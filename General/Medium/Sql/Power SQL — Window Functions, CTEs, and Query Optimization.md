## (Part 3 from the series: “Master SQL Once and For All”)

If Part 2 taught you how to **get data**, this part teaches you how to **analyze it like a pro**.

In real-world systems — dashboards, analytics, or reports — you often need to:

- Rank users by performance
- Find running totals or moving averages
- Simplify complex subqueries
- Speed up slow queries

That’s where **advanced SQL** features like **Window Functions**, **CTEs**, and **Indexes** become your best friends.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*zdEkwqI9N06tFUBGJ7NncA.png)

## 1. Common Table Expressions (CTEs) — Make Queries Clean and Readable

As your queries grow complex, subqueries become hard to read and debug.  
That’s exactly what **CTEs** are meant to fix.

A **CTE** lets you create a _temporary, named result set_ that you can refer to later in the same query.

It starts with the `WITH` keyword:

```sql
WITH department_avg AS (  
    SELECT department, AVG(salary) AS avg_salary  
    FROM employees  
    GROUP BY department  
)  
SELECT name, department, salary  
FROM employees e  
JOIN department_avg d  
ON e.department = d.department  
WHERE e.salary > d.avg_salary;
```

What’s happening here:

- The CTE (`department_avg`) calculates the average salary per department.
- The main query then uses that result to fetch employees earning above their department’s average.

### **Why it’s valuable:**

- Makes queries more readable
- Easier to debug than nested subqueries
- Can be reused multiple times in the same query

## 2. Window Functions — Analytics in One Line

Window functions are like magic: they let you perform calculations **across rows**, but **without collapsing them into groups**.

That means you can rank, sum, or average values while still keeping each individual row visible.

Here’s the general form:

```sql
function_name(expression) OVER (  
  PARTITION BY column  
  ORDER BY column  
)
```

### Example 1: Ranking Employees by Salary

```sql
SELECT name, department, salary,  
       RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank_in_dept  
FROM employees;
```

This gives each employee a rank within their department.  
The highest-paid employee in each department gets rank 1.

### Example 2: Running Total

```sql
SELECT name, department, salary,  
       SUM(salary) OVER (PARTITION BY department ORDER BY name) AS running_total  
FROM employees;
```

This shows a running total of salaries within each department.

### Example 3: Comparing Each Row with the Average

```sql
SELECT name, department, salary,  
       AVG(salary) OVER (PARTITION BY department) AS dept_avg,  
       salary - AVG(salary) OVER (PARTITION BY department) AS difference  
FROM employees;
```

Now you can instantly see who earns above or below their department’s average — without needing a join or a subquery.

### **Why it’s valuable:**

- Cleaner analytics with fewer subqueries
- Ideal for reports and dashboards
- Supported in all major databases (PostgreSQL, MySQL 8+, SQL Server, Oracle)

## 3. CTE + Window Function Combo

Let’s say you’re asked:

> _“Find the top 3 earners in each department.”_

Here’s how to do it elegantly:

```sql
WITH ranked_employees AS (  
  SELECT name, department, salary,  
     RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rnk  
  FROM employees  
)  
SELECT name, department, salary  
FROM ranked_employees  
WHERE rnk <= 3;
```

That’s clean, powerful, and perfectly readable.  
No nested SELECT nightmares — just logic that speaks for itself.

## 4. Query Optimization

Writing a correct query is one thing. Writing an _efficient_ one is another.

Even a simple query can slow down a production system if it scans millions of unnecessary rows.  
Let’s fix that.

### 1. Use Indexes Smartly

An **index** is like a book’s table of contents — it helps the database jump straight to the right pages instead of reading every one.

You can create one like this:

```sql
CREATE INDEX idx_salary ON employees(salary);
```

Use indexes on columns that are:

- Frequently used in `WHERE` filters
- Used in `JOIN` conditions
- Involved in `ORDER BY`

But be careful:  
Too many indexes slow down inserts and updates, because every change must also update all relevant indexes.

### 2. Avoid SELECT *

It’s tempting to select everything, but fetching all columns means:

- More data transferred
- More I/O
- Slower query performance

Always specify the columns you need:

```sql
SELECT name, salary FROM employees;
```

### 3. Use EXISTS Instead of IN for Large Subqueries

```sql
-- Slower  
SELECT name FROM employees  
WHERE department_id IN (SELECT id FROM departments WHERE active = 1);  
-- Faster  
SELECT name FROM employees e  
WHERE EXISTS (  
  SELECT 1 FROM departments d  
  WHERE e.department_id = d.id AND d.active = 1  
);
```

`EXISTS` stops scanning as soon as a match is found, making it faster in many databases.

### 4. Use EXPLAIN (or Execution Plan)

Most SQL engines let you see **how** your query runs internally.

In PostgreSQL:

```sql
EXPLAIN ANALYZE SELECT * FROM employees WHERE salary > 80000;
```

It shows whether your query uses indexes, how many rows it scans, and which operations are slow.

This is gold for performance tuning.

### 5. Limit Results Early

Always use `WHERE` or `LIMIT` to restrict what you fetch.

It reduces memory usage and response time, especially in analytics systems.

## 5. Caching and Materialized Views

If you repeatedly run heavy aggregation queries — say, for dashboards — use **Materialized Views**.

They store query results like a snapshot, making future access much faster.

```sql
CREATE MATERIALIZED VIEW department_summary AS  
SELECT department, COUNT(*) AS total_employees, AVG(salary) AS avg_salary  
FROM employees  
GROUP BY department;
```

You can refresh it when needed:

```sql
REFRESH MATERIALIZED VIEW department_summary;
```

This is perfect for analytics that don’t need real-time freshness.

## Quick Practice Tasks

1. Use a CTE to find departments with an above-average total salary.
2. Use a window function to find each employee’s percentile rank by salary.
3. Check how your query plan changes when you add an index.

## Wrapping Up

At this stage, you’ve gone beyond basic SQL.

You’re now working with **analytic-grade SQL** — the kind used by data engineers, analysts, and backend developers daily.

You learned how to:

- Simplify queries using **CTEs**
- Analyze data dynamically using **Window Functions**
- Optimize queries with **Indexes** and smart techniques
- Leverage **Materialized Views** for performance boosts

This is where SQL starts feeling _powerful_ — not just for CRUD operations, but for deep, elegant data analysis.

## Coming Next:

**Part 4 — Advanced SQL Design & Data Integrity: Triggers, Transactions, and Constraints**

This next part will focus on **how to keep your database consistent, secure, and self-maintaining**, using SQL’s more advanced mechanisms.

[

Sql

](https://medium.com/tag/sql?source=post_page-----137113e03fcf---------------------------------------)

[

Sql Queries

](https://medium.com/tag/sql-queries?source=post_page-----137113e03fcf---------------------------------------)

[

Database

](https://medium.com/tag/database?source=post_page-----137113e03fcf---------------------------------------)

[

Database Administration

](https://medium.com/tag/database-administration?source=post_page-----137113e03fcf---------------------------------------)

[

Relational Databases

](https://medium.com/tag/relational-databases?source=post_page-----137113e03fcf---------------------------------------)

2

[

![Ujjawal Rohra](https://miro.medium.com/v2/resize:fill:96:96/0*GWj80XcXPgW442aj)



](https://medium.com/@ujjawalr?source=post_page---post_author_info--137113e03fcf---------------------------------------)

[

## Written by Ujjawal Rohra

](https://medium.com/@ujjawalr?source=post_page---post_author_info--137113e03fcf---------------------------------------)

[1.6K followers](https://medium.com/@ujjawalr/followers?source=post_page---post_author_info--137113e03fcf---------------------------------------)

·[1 following](https://medium.com/@ujjawalr/following?source=post_page---post_author_info--137113e03fcf---------------------------------------)

Following
