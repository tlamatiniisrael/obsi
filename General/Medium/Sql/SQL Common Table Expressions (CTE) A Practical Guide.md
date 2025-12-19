# SQL Common Table Expressions (CTE): A Practical Guide

![](https://miro.medium.com/v2/resize:fit:1400/1*iaKGeizhihI3dAmV8Ma3uQ.png)

Over the years I’ve come across many complex SQL queries that are hard to read and maintain. One of the most powerful features in SQL that helps to tackle this problem is the Common Table Expression, or CTE. CTEs, provide a way to create temporary, named result sets that you can reference within a larger SQL statement. This makes your queries more readable, maintainable, and even allows for recursive queries, which are otherwise very difficult to write in SQL.

You can also read the same blog on my [website](https://codewiz.info/blog/sql-common-table-expressions/)

## What is a Common Table Expression (CTE)?

A Common Table Expression (CTE) is a temporary named result set that you can reference within a `SELECT`, `INSERT`, `UPDATE`, or `DELETE` statement. A CTE is defined using the `WITH` clause, followed by the name of the CTE and the query that defines it.

The basic syntax for a CTE is as follows:

The basic syntax for a CTE is as follows:

```sql
WITH cte_name (column1, column2, ...) AS (  
    -- The query that defines the CTE  
    SELECT ...  
)  
-- The main query that uses the CTE  
SELECT ...  
FROM cte_name;
```

You can also define multiple CTEs in a single `WITH` clause, separated by commas:

```sql
WITH  
cte_name1 AS (  
    SELECT ...  
),  
cte_name2 AS (  
    -- This CTE can reference cte_name1  
    SELECT ...  
    FROM cte_name1  
)  
SELECT ...  
FROM cte_name2;
```

Now, let’s look at some scenarios where CTEs are particularly useful.

## When to Use a CTE?

CTEs are versatile and can be used in various scenarios. Let’s explore some of the most common use cases with examples. For our examples, we’ll use a simple database schema for a blog with `users`, `posts`, and `comments` tables.

Here’s the schema:

### `users`

- `id` (INT, PRIMARY KEY)
- `name` (VARCHAR)
- `join_date` (DATE)

### `posts`

- `id` (INT, PRIMARY KEY)
- `title` (VARCHAR)
- `content` (TEXT)
- `user_id` (INT, FOREIGN KEY to `users.id`)

### `comments`

- `id` (INT, PRIMARY KEY)
- `comment` (TEXT)
- `post_id` (INT, FOREIGN KEY to `posts.id`)
- `user_id` (INT, FOREIGN KEY to `users.id`)

**1. Simplifying Complex Queries**

One of the most common uses of CTEs is to break down complex queries into smaller, more manageable parts. This improves readability and makes the query easier to understand and debug.

**Scenario:** Find the top 3 most active users who have written more than 5 posts and also commented on more than 10 posts.

Without a CTE, you might end up with a query with multiple subqueries, which can be hard to follow:

```sql
SELECT  
    u.id,  
    u.name,  
    post_counts.post_count,  
    comment_counts.comment_count  
FROM users u  
JOIN (  
    SELECT user_id, COUNT(*) as post_count  
    FROM posts  
    GROUP BY user_id  
) AS post_counts ON u.id = post_counts.user_id  
JOIN (  
    SELECT user_id, COUNT(*) as comment_count  
    FROM comments  
    GROUP BY user_id  
) AS comment_counts ON u.id = comment_counts.user_id  
WHERE post_counts.post_count > 5 AND comment_counts.comment_count > 10  
ORDER BY (post_counts.post_count + comment_counts.comment_count) DESC  
LIMIT 3;
```

==This query is already getting a bit complex. Let’s see how we can simplify it using CTEs.==

```sql
WITH  
post_counts AS (  
    SELECT user_id, COUNT(*) as post_count  
    FROM posts  
    GROUP BY user_id  
),  
comment_counts AS (  
    SELECT user_id, COUNT(*) as comment_count  
    FROM comments  
    GROUP BY user_id  
)  
SELECT  
    u.id,  
    u.name,  
    pc.post_count,  
    cc.comment_count  
FROM users u  
JOIN post_counts pc ON u.id = pc.user_id  
JOIN comment_counts cc ON u.id = cc.user_id  
WHERE pc.post_count > 5 AND cc.comment_count > 10  
ORDER BY (pc.post_count + cc.comment_count) DESC  
LIMIT 3;
```

As you can see, the version with CTEs is much more readable. Each part of the logic is separated into its own named block, making the final `SELECT` statement much cleaner and easier to understand.

## 2. Recursive Queries

CTEs are the standard way to write recursive queries in SQL. This is particularly useful for working with hierarchical data, such as organizational charts, file systems, or nested comments.

A recursive CTE has a specific structure:

1. **Anchor Member:** The initial query that returns the base result set of the recursion.
2. **Recursive Member:** The query that references the CTE itself and is repeatedly executed until it returns no more rows.
3. **UNION ALL:** The operator that combines the anchor and recursive members.

**Scenario:** Imagine we have a `employees` table with a `manager_id` that references the `id` of another employee. We want to find the entire hierarchy of employees under a specific manager.

Here’s the `employees` table schema:

- `id` (INT, PRIMARY KEY)
- `name` (VARCHAR)
- `manager_id` (INT, FOREIGN KEY to `employees.id`)

Let’s say we want to find all employees under the CEO, whose `id` is 1.

```sql
WITH RECURSIVE employee_hierarchy AS (  
    -- Anchor member: select the top-level employee (the CEO)  
    SELECT  
        id,  
        name,  
        manager_id,  
        0 AS level  
    FROM employees  
    WHERE id = 1  
UNION ALL  
    -- Recursive member: join the CTE with the employees table  
    -- to find the employees who report to the previous level  
    SELECT  
        e.id,  
        e.name,  
        e.manager_id,  
        eh.level + 1  
    FROM employees e  
    JOIN employee_hierarchy eh ON e.manager_id = eh.id  
)  
SELECT * FROM employee_hierarchy;
```

In this example:

- The **anchor member** selects the CEO (employee with `id` = 1) and sets the initial `level` to 0.
- The **recursive member** joins the `employees` table with the `employee_hierarchy` CTE to find the direct reports of the employees found in the previous step. It also increments the `level` for each level of the hierarchy.
- The `UNION ALL` operator combines the results of the anchor and recursive members.

This query will traverse the entire hierarchy and return a list of all employees under the CEO, along with their level in the hierarchy.

## Conclusion

Common Table Expressions are a powerful and versatile feature in modern SQL that can help you write cleaner, more readable, and more maintainable queries. By allowing you to break down complex queries into smaller, logical units and by providing a standard way to write recursive queries, CTEs can significantly improve your effectiveness as a developer.

For more in-depth tutorials on software development, follow me:

🔗 [Blog](https://codewiz.info/) 🔗 [YouTube](https://www.youtube.com/@Code.Wizzard) 🔗 [LinkedIn](https://www.linkedin.com/in/code-wiz-740370302/) 🔗 [Medium](https://medium.com/@code.wizzard01) 🔗 [Github](https://github.com/CodeWizzard01)

Stay tuned for more content on the latest in AI and software engineering!