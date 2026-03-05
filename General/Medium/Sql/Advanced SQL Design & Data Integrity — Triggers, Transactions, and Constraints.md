## (Part 4 from the series: “Master SQL Once and For All”)

## Why Data Integrity Matters

A database isn’t just a place to store data — it’s where truth lives.

If that truth gets corrupted (duplicate users, broken relations, invalid updates), everything downstream — reports, dashboards, business logic — starts to fail.

SQL gives you tools to **protect** that truth automatically: **constraints**, **transactions**, and **triggers**.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*zdEkwqI9N06tFUBGJ7NncA.png)

## 1. Constraints

A **constraint** is simply a rule that ensures only valid data enters the table.

They work silently in the background, enforcing structure and logic.

```sql
CREATE TABLE employees (  
  id INT PRIMARY KEY,  
  name VARCHAR(100) NOT NULL,  
  email VARCHAR(255) UNIQUE,  
  department_id INT,  
  salary DECIMAL(10,2) CHECK (salary > 0),  
  FOREIGN KEY (department_id) REFERENCES departments(id)  
);
```

Let’s understand these:

- **PRIMARY KEY** — Ensures each row is uniquely identifiable.
- **NOT NULL** — Prevents null values.
- **UNIQUE** — Stops duplicate entries (like emails).
- **CHECK** — Validates a condition before insertion or update.
- **FOREIGN KEY** — Links tables together, maintaining relationships.

Foreign keys enforce _referential integrity_ — no employee can belong to a non-existent department.

## 2. Transactions

Imagine you’re transferring money between two accounts: you must debit one and credit another.

If one succeeds and the other fails, the data becomes inconsistent.

A **transaction** ensures both operations either happen **together** or **not at all**.

```sql
BEGIN;  
UPDATE accounts SET balance = balance - 500 WHERE id = 1;  
UPDATE accounts SET balance = balance + 500 WHERE id = 2;  
COMMIT;
```

If something goes wrong before `COMMIT`, you can roll everything back:

```sql
ROLLBACK;
```

### **Key properties (ACID):**

- **Atomicity** — a transaction is treated as a single, indivisible unit of work.
- **Consistency** — Rules and constraints remain valid.
- **Isolation** — Parallel transactions don’t interfere.
- **Durability** — Once committed, changes persist even after crashes.

## Real-World Use Case

You’re building an e-commerce app. When an order is placed:

1. Deduct inventory
2. Charge payment
3. Create order record

All three must succeed together.

Wrap them inside a transaction to prevent partial updates.

## 3. Triggers

A **trigger** automatically executes SQL logic in response to certain events like `INSERT`, `UPDATE`, or `DELETE`.

They’re perfect for maintaining audit trails, updating derived values, or enforcing advanced business rules.

```sql
CREATE TRIGGER update_timestamp  
BEFORE UPDATE ON employees  
FOR EACH ROW  
EXECUTE FUNCTION set_last_modified();
```

The function might look like:

```sql
CREATE FUNCTION set_last_modified()  
RETURNS TRIGGER AS $$  
BEGIN  
   NEW.last_modified = NOW();  
   RETURN NEW;  
END;  
$$ LANGUAGE plpgsql;
```

Every time an employee record updates, `last_modified` is refreshed automatically.  
No application code needed.

## Another Example

```sql
CREATE TRIGGER prevent_salary_cut  
BEFORE UPDATE ON employees  
FOR EACH ROW  
WHEN (NEW.salary < OLD.salary)  
EXECUTE FUNCTION raise_salary_error();
```

This trigger can raise an exception or log the attempt — ensuring no salary drops without explicit approval.

### **Use triggers wisely:**

They’re powerful but can hide logic that’s hard to debug.  
Keep them for _auditing_ or _consistency_, not complex workflows.

## Putting It All Together

Let’s combine everything we’ve learned into a single use case.

### Scenario: Employee Database

You need to:

- Maintain accurate department–employee relationships
- Prevent invalid data
- Keep audit trails automatically
- Ensure atomic updates

Here’s how:

```sql
BEGIN;  
CREATE TABLE departments (  
    id SERIAL PRIMARY KEY,  
    name VARCHAR(100) UNIQUE NOT NULL  
);  
CREATE TABLE employees (  
  id SERIAL PRIMARY KEY,  
  name VARCHAR(100) NOT NULL,  
  email VARCHAR(255) UNIQUE NOT NULL,  
  department_id INT REFERENCES departments(id),  
  salary DECIMAL(10,2) CHECK (salary > 0),  
  last_modified TIMESTAMP DEFAULT NOW()  
);  
CREATE FUNCTION update_modified_time()  
RETURNS TRIGGER AS $$  
BEGIN  
  NEW.last_modified = NOW();  
  RETURN NEW;  
END;  
$$ LANGUAGE plpgsql;  
CREATE TRIGGER trg_update_time  
BEFORE UPDATE ON employees  
FOR EACH ROW  
EXECUTE FUNCTION update_modified_time();  
COMMIT;
```

What this setup achieves:

- Data validity (via constraints)
- Consistency across updates (via transactions)
- Automatic timestamp updates (via triggers)

All without writing a single line of extra app logic.

### Note:

You might notice the line `LANGUAGE plpgsql;` at the end of the function.

This tells PostgreSQL that the code inside the `$$ ... $$` block uses **PL/pgSQL** — PostgreSQL’s _procedural_ language.

PL/pgSQL extends normal SQL with programming-like features such as `BEGIN ... END`, variables, loops, and conditional logic.

Without this line, PostgreSQL wouldn’t know how to interpret statements like `NEW.last_modified = NOW();`.

If your function only runs a single plain SQL statement (for example, `SELECT COUNT(*) FROM employees;`), you can instead use:

```sql
LANGUAGE sql;
```

But whenever your function contains procedural logic or interacts with trigger variables (`NEW`, `OLD`),  
you must use `LANGUAGE plpgsql;`.

### **What if you’re using another database?**

Every major DBMS has its own procedural extension:

- **MySQL / MariaDB  
    **Use `BEGIN ... END` blocks directly inside triggers or stored procedures — no need for a language declaration.
- **SQL Server  
    **Uses **T-SQL (Transact-SQL)**, Microsoft’s procedural extension; you define stored procedures or triggers using `CREATE PROCEDURE` or `CREATE TRIGGER` statements.
- **Oracle  
    **Uses **PL/SQL**, which is similar to PL/pgSQL but Oracle-specific. You don’t need a `LANGUAGE` clause there either.

So, the `LANGUAGE plpgsql;` line is **PostgreSQL-specific syntax**.

The overall logic — like setting `last_modified` automatically — remains the same across databases, but the _syntax_ for writing triggers and procedural code varies slightly between systems.

## Best Practices for Reliable Database Design

- Always define **primary keys** and **foreign keys** explicitly.
- Use **CHECK** constraints to validate business rules close to the data.
- Wrap critical multi-step operations in **transactions**.
- Create **audit triggers** for accountability, not business flow control.
- Keep triggers lightweight and well-documented.
- Regularly test rollback scenarios before going live.

These habits separate a casual SQL user from a true database engineer.

## Final Thoughts

You’ve now walked through the full landscape of SQL:

- **Part 1:** Understanding how databases are built (DDL, DML, DCL, TCL)
- **Part 2:** Mastering queries and joins
- **Part 3:** Writing analytic-grade SQL with CTEs and window functions
- **Part 4:** Enforcing data integrity with constraints, triggers, and transactions

At this point, you’re not just writing queries — you’re **designing dependable systems**.

SQL isn’t just syntax; it’s the foundation of every reliable data platform.

Keep practicing, experiment with your own schemas, and you’ll soon see how these fundamentals apply in any modern database environment.

[

Database

](https://medium.com/tag/database?source=post_page-----aca98d4c8e73---------------------------------------)

[

Relational Databases

](https://medium.com/tag/relational-databases?source=post_page-----aca98d4c8e73---------------------------------------)

[

Sql

](https://medium.com/tag/sql?source=post_page-----aca98d4c8e73---------------------------------------)

[

Database Administration

](https://medium.com/tag/database-administration?source=post_page-----aca98d4c8e73---------------------------------------)

[

Sql Queries

](https://medium.com/tag/sql-queries?source=post_page-----aca98d4c8e73---------------------------------------)

10

[

![Ujjawal Rohra](https://miro.medium.com/v2/resize:fill:96:96/0*GWj80XcXPgW442aj)



](https://medium.com/@ujjawalr?source=post_page---post_author_info--aca98d4c8e73---------------------------------------)

[

## Written by Ujjawal Rohra

](https://medium.com/@ujjawalr?source=post_page---post_author_info--aca98d4c8e73---------------------------------------)

[1.6K followers](https://medium.com/@ujjawalr/followers?source=post_page---post_author_info--aca98d4c8e73---------------------------------------)

·[1 following](https://medium.com/@ujjawalr/following?source=post_page---post_author_info--aca98d4c8e73---------------------------------------)

Following