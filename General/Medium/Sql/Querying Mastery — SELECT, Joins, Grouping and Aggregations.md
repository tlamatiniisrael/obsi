## (Part 2 From the series: “Master SQL Once and For All”)

If the first part of this series taught you to **build** a database, this one teaches you to **talk to it**.

SQL’s real power lies in its ability to answer questions:

- “Which customers spent the most last month?”
- “How many users signed up from each city?”
- “What’s the average salary per department?”

These answers don’t come from magic — they come from **queries**, and every query begins with one powerful word:

> **_SELECT_**

Let’s explore how to truly learn it.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*zdEkwqI9N06tFUBGJ7NncA.png)

## 1. The SELECT Statement — Your Window into the Data

The `SELECT` command retrieves data from one or more tables.

Here’s its most basic form:

```sql
SELECT column1, column2  
FROM table_name;
```

To get every column:

```sql
SELECT * FROM employees;
```

That `*` means “show me everything.”

It’s fine for quick checks, but in real applications, it’s better to be explicit — only ask for what you need.

### Adding Filters with WHERE

You rarely want _all_ rows. The `WHERE` clause lets you filter data.

```sql
SELECT name, department, salary  
FROM employees  
WHERE salary > 75000;
```

You can combine conditions:

```sql
SELECT name, department  
FROM employees  
WHERE department = 'IT' AND salary > 90000;
```

Common operators include:  
`=`, `!=`, `<`, `>`, `<=`, `>=`, `BETWEEN`, `LIKE`, `IN`, `IS NULL`.

Example using LIKE:

```sql
SELECT name  
FROM employees  
WHERE name LIKE 'A%';
```

This finds all names starting with “A.”

### Sorting with ORDER BY

To arrange your results, use `ORDER BY`:

```sql
SELECT name, salary  
FROM employees  
ORDER BY salary DESC;
```

- `ASC` = ascending (default)
- `DESC` = descending

### Limiting Results

Sometimes you just want a few rows.

```sql
SELECT * FROM employees LIMIT 5;
```

In SQL Server, use:

```sql
SELECT TOP 5 * FROM employees;
```

## 2. Combining Data — The Power of Joins

Real-world data doesn’t live in one table.

You might have an **employees** table and a **departments** table — related but separate.

To see meaningful results, you join them.

### INNER JOIN — Matching Records in Both

```sql
SELECT e.name, e.salary, d.department_name  
FROM employees e  
INNER JOIN departments d  
ON e.department_id = d.id;
```

This returns only employees who have a matching department.

Think of it like: “Give me the intersection of these two lists.”

### LEFT JOIN — Keep Everything from the Left

```sql
SELECT e.name, d.department_name  
FROM employees e  
LEFT JOIN departments d  
ON e.department_id = d.id;
```

You’ll get all employees, even those without a department.  
For missing matches, SQL fills in `NULL`.

### RIGHT JOIN and FULL JOIN

`RIGHT JOIN` keeps all from the right table (and matching ones from the left).

`FULL JOIN` combines everything from both sides, filling gaps with `NULL`.

Not all databases support `FULL JOIN` directly — PostgreSQL does, MySQL added it in newer versions, and older versions may need workarounds using `UNION`.

### CROSS JOIN — Every Combination

Rarely used but interesting — it combines every row from one table with every row from another.

```sql
SELECT e.name, p.project_name  
FROM employees e  
CROSS JOIN projects p;
```

If you have 10 employees and 3 projects, you’ll get 30 rows.

## 3. Grouping and Aggregation — Turning Rows into Insights

When you want to summarize data — count records, find averages, totals, or max/min values — you use **aggregate functions** with **GROUP BY**.

### Common Aggregate Functions

```sql
SELECT COUNT(*) FROM employees;          -- total number of employees  
SELECT AVG(salary) FROM employees;       -- average salary  
SELECT MAX(salary), MIN(salary) FROM employees;  
SELECT SUM(salary) FROM employees;       -- total salary payout
```

### Using GROUP BY

Say you want the average salary per department:

```sql
SELECT department, AVG(salary) AS avg_salary  
FROM employees  
GROUP BY department;
```

Each department becomes a single row showing its average salary.

### Filtering Groups with HAVING

`WHERE` filters individual rows before grouping.  
`HAVING` filters groups after aggregation.

```sql
SELECT department, AVG(salary) AS avg_salary  
FROM employees  
GROUP BY department  
HAVING AVG(salary) > 80000;
```

This shows only departments whose average salary is above 80 k.

## 4. Subqueries and Nested SELECTs

A **subquery** is a query inside another query.  
It’s useful when one answer depends on another.

Example: find employees earning above the company average:

```sql
SELECT name, salary  
FROM employees  
WHERE salary > (  
    SELECT AVG(salary)  
    FROM employees  
);
```

SQL runs the inner query first, then uses that result in the outer one.

## 5. Views — Reusable Queries

If you frequently run the same query, turn it into a **view**.

A view behaves like a virtual table — you can query it just like a regular one.

```sql
CREATE VIEW high_earners AS  
SELECT name, department, salary  
FROM employees  
WHERE salary > 100000;
```

Now, simply use:

```sql
SELECT * FROM high_earners;
```

Views are great for simplifying complex joins or for hiding sensitive columns from certain users.

## 6. Dialect Differences to Keep in Mind

SQL is standardized, but every database adds its own flavor.

Here are a few common differences to remember:

**Auto-Increment Columns:**

- MySQL → `AUTO_INCREMENT`
- PostgreSQL → `SERIAL` or `GENERATED AS IDENTITY`
- SQL Server → `IDENTITY(1,1)`

**Limiting Rows:**

- MySQL / PostgreSQL → `LIMIT n`
- SQL Server → `TOP n`

**String Concatenation:**

- MySQL → `CONCAT()`
- PostgreSQL → `||`
- SQL Server → `+`

Keep these nuances in mind when switching between databases.

## Try This Challenge

1. Write a query to find the top 3 highest-paid employees in each department.
2. List all departments that have more than 5 employees.
3. Find employees whose salary is higher than the average of their own department.

Each of these problems forces you to combine what you’ve learned — joins, grouping, and subqueries — and that’s where SQL starts feeling powerful.

## Wrapping Up

By now, you’ve stepped into the **core of SQL** — the part that transforms raw data into insights.

You learned how to:

- Retrieve and filter data with `SELECT` and `WHERE`
- Combine tables with different types of JOINs
- Summarize information using aggregations and grouping
- Create views for reusability and clarity
- Handle dialect differences confidently

In the next part, we’ll take SQL even further — exploring **window functions, CTEs, indexes, and query optimization** — the advanced techniques that separate capable users from true SQL pros.

[

Sql

](https://medium.com/tag/sql?source=post_page-----dc9543faf8e0---------------------------------------)

[

Database

](https://medium.com/tag/database?source=post_page-----dc9543faf8e0---------------------------------------)

[

Database Administration

](https://medium.com/tag/database-administration?source=post_page-----dc9543faf8e0---------------------------------------)

[

Database Design

](https://medium.com/tag/database-design?source=post_page-----dc9543faf8e0---------------------------------------)

[

Sql Queries

](https://medium.com/tag/sql-queries?source=post_page-----dc9543faf8e0---------------------------------------)

2

1

[

![Ujjawal Rohra](https://miro.medium.com/v2/resize:fill:96:96/0*GWj80XcXPgW442aj)



](https://medium.com/@ujjawalr?source=post_page---post_author_info--dc9543faf8e0---------------------------------------)

[

## Written by Ujjawal Rohra

](https://medium.com/@ujjawalr?source=post_page---post_author_info--dc9543faf8e0---------------------------------------)

[1.6K followers](https://medium.com/@ujjawalr/followers?source=post_page---post_author_info--dc9543faf8e0---------------------------------------)

·[1 following](https://medium.com/@ujjawalr/following?source=post_page---post_author_info--dc9543faf8e0---------------------------------------)

Following
