
# The SQL Universe: Understanding DDL, DML, DCL, and TCL
## (Part 1 of the “Master SQL Once and For All” Series)

## What Exactly Is SQL?

Imagine you’re working at a company that stores thousands of employee records — names, salaries, departments, joining dates, and more.

Now you need to answer simple questions like:

- “Who earns more than $80,000?”
- “How many employees joined last month?”
- “Add this new hire’s record.”
- “Delete an old entry.”

You could open a spreadsheet, scroll endlessly, and try to filter data manually.

Or, you could **talk directly to the database** and get precise answers in seconds.

That’s what **SQL (Structured Query Language)** is — a **language to communicate with databases**.

It lets you ask questions, modify data, and control access using simple, English-like commands.

No matter which database you use — MySQL, PostgreSQL, SQL Server, Oracle, or SQLite — SQL remains the common foundation.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*zdEkwqI9N06tFUBGJ7NncA.png)

## Understanding the SQL Universe

SQL might look like one big language, but it’s actually made up of **four smaller languages**, each serving a specific purpose:

- **DDL — Data Definition Language** (building the structure)
- **DML — Data Manipulation Language** (working with the data)
- **DCL — Data Control Language** (managing access)
- **TCL — Transaction Control Language** (ensuring data safety)

These four together form the **core of SQL** — once you understand them, everything else becomes easier.

## 1. DDL — Data Definition Language

**DDL defines the structure of your database.**

Think of it as the blueprint of your data world — you’re deciding what your tables look like and what kind of information they will hold.

Let’s say you want to store employee details. You’d start with a `CREATE TABLE` command:

```sql
CREATE TABLE employees (  
    id INT PRIMARY KEY,  
    name VARCHAR(100) NOT NULL,  
    department VARCHAR(50),  
    salary DECIMAL(10, 2),  
    join_date DATE  
);
```

You just created a table named **employees** with columns for ID, name, department, salary, and joining date.

If you later realize you missed an **email** column, you can modify the table:

```sql
ALTER TABLE employees  
ADD email VARCHAR(100);
```

And if you no longer need a table at all, you can remove it:

```sql
DROP TABLE old_employees;
```

If you only want to **clear all the data** but keep the structure, use this:

```sql
TRUNCATE TABLE employees;
```

> `_DROP_` _removes both the table and its data permanently._`_TRUNCATE_` _wipes only the data, keeping the table ready for reuse._

## 2. DML — Data Manipulation Language

Now that your structure exists, it’s time to **work with the data inside it.**

DML is what you use most of the time as a developer or data analyst.

It includes commands like `INSERT`, `SELECT`, `UPDATE`, and `DELETE`.

Let’s add some employee records:

```sql
INSERT INTO employees (id, name, department, salary)  
VALUES (1, 'Alice', 'HR', 70000),  
       (2, 'Bob', 'IT', 85000);
```

Now, suppose you need to increase salaries for IT employees by 10%:

```sql
UPDATE employees  
SET salary = salary * 1.10  
WHERE department = 'IT';
```

Or, if someone leaves the company:

```sql
DELETE FROM employees  
WHERE id = 2;
```

And when you simply want to **see the data**, you use `SELECT`:

```sql
SELECT name, department, salary  
FROM employees  
WHERE salary > 75000  
ORDER BY salary DESC;
```

That’s a query asking, “Show me all employees earning more than 75,000, sorted by salary.”

> _Before deleting or updating data, run the same condition with a_ `_SELECT_` _query first.  
> It helps confirm that you’re targeting the right rows._

## 3. DCL — Data Control Language

Databases often have multiple users — developers, analysts, testers.  
Not everyone should have the same access.

**DCL manages who can do what.**

Let’s say you want to allow an analyst to view and insert data into the employees table but not delete anything:

```sql
GRANT SELECT, INSERT  
ON employees  
TO analyst_user;
```

If you later decide to revoke delete permissions (or any permission you previously gave):

```sql
REVOKE DELETE  
ON employees  
FROM analyst_user;
```

This layer ensures your data remains safe even in shared environments.

## 4. TCL — Transaction Control Language

Imagine transferring money between two accounts:  
You subtract from one account and add to another.

But what if your system crashes after the first operation and before the second?

One account loses money, the other doesn’t gain it — that’s inconsistent data.

**TCL** prevents such problems by making operations **atomic** — all succeed, or none do.

Here’s how it works:

```sql
BEGIN;  
UPDATE accounts  
SET balance = balance - 500  
WHERE id = 101;  
UPDATE accounts  
SET balance = balance + 500  
WHERE id = 202;  
COMMIT;
```

If everything runs successfully, `COMMIT` saves the changes permanently.

But if anything fails midway, you can undo everything:

```sql
ROLLBACK;
```

This is what makes databases reliable.

Transactions ensure **ACID** properties —

- **A**tomicity,
- **C**onsistency,
- **I**solation,
- **D**urability

meaning your data always remains accurate and safe.

## How These Four Fit Together

Think of the SQL universe as a complete ecosystem:

1. **DDL** creates the structure (like setting up rooms in a house).
2. **DML** fills those rooms with data (furniture, decor, everything inside).
3. **DCL** decides who can enter which room.
4. **TCL** makes sure if something goes wrong while rearranging things, you can roll everything back safely.

Every project, big or small, uses these four in combination.

## Try This Small Exercise

Here’s a tiny project to practice what you just learned:

```sql
CREATE TABLE bank_accounts (  
    acc_no INT PRIMARY KEY,  
    holder VARCHAR(100),  
    balance DECIMAL(10, 2)  
);  
  
INSERT INTO bank_accounts VALUES  
(101, 'Alice', 5000),  
(102, 'Bob', 7000);  
BEGIN;  
UPDATE bank_accounts SET balance = balance - 1000 WHERE acc_no = 101;  
UPDATE bank_accounts SET balance = balance + 1000 WHERE acc_no = 102;  
COMMIT;
```

Now, run it again — but before the second update, force an error (like referring to a wrong column name) and see how `ROLLBACK` undoes everything.

That’s the power of transactions.

## Wrapping Up

You’ve just learned the **four building blocks of SQL** — the foundation on which everything else stands.

- **DDL** defines the structure
- **DML** manipulates the data
- **DCL** secures access
- **TCL** protects data consistency

In the next part, we’ll move beyond structure and control — and dive into **the art of querying**:

- how to fetch exactly the data you want,
- combine multiple tables, and
- turn raw records into meaningful insights.

[

Sql

](https://medium.com/tag/sql?source=post_page-----5c5f5342139e---------------------------------------)

[

Database

](https://medium.com/tag/database?source=post_page-----5c5f5342139e---------------------------------------)

[

Relational Databases

](https://medium.com/tag/relational-databases?source=post_page-----5c5f5342139e---------------------------------------)

[

Structured Query Language

](https://medium.com/tag/structured-query-language?source=post_page-----5c5f5342139e---------------------------------------)

[

Database Administration

](https://medium.com/tag/database-administration?source=post_page-----5c5f5342139e---------------------------------------)

[

![Ujjawal Rohra](https://miro.medium.com/v2/resize:fill:96:96/0*GWj80XcXPgW442aj)



](https://medium.com/@ujjawalr?source=post_page---post_author_info--5c5f5342139e---------------------------------------)

[

## Written by Ujjawal Rohra

](https://medium.com/@ujjawalr?source=post_page---post_author_info--5c5f5342139e---------------------------------------)

[1.6K followers](https://medium.com/@ujjawalr/followers?source=post_page---post_author_info--5c5f5342139e---------------------------------------)

·[1 following](https://medium.com/@ujjawalr/following?source=post_page---post_author_info--5c5f5342139e---------------------------------------)

Following