
![](https://miro.medium.com/v2/resize:fit:1400/1*ke6wfmD-v4a6Ul6I_3BEVw.png)

> 👉**If** [**you are not a Member — Read for free**](https://medium.com/@kotiavula6/this-sql-question-exposes-fake-senior-developers-most-people-get-it-wrong-ce8a6a94952d?sk=fcbed73d36c2fa6358f044d5ab1c1801) **here :**

If you think SQL interviews are easy, wait until you face _this_ question.

I’ve seen experienced developers, backend engineers, data engineers, and even senior architects get confused by it. Not because the question is tricky, but because SQL behaves differently from what most people _think_.

Today, let’s break it down in a simple, friendly, honest way — with zero shortcuts and clear explanations.

And yes… after reading this, you’ll never fear SQL interviews again.

## The SQL Question That Traps 99% of Developers

**“What will be the output of this query?”**

```sql
SELECT name, salary  
FROM Employees  
WHERE salary > 5000  
AND department_id IN (  
    SELECT department_id  
    FROM Departments  
    WHERE location = 'London'  
);
```

On the surface, it looks simple:

- Filter employees with salary > 5000
- Only include employees working in London-based departments

But here’s the twist.

Most developers answer correctly _only when the subquery returns normal values_.  
But the interviewer isn’t checking that.

They’re actually testing your knowledge of **NULL** behavior inside subqueries.

## The Hidden Trap: NULL in a Subquery

Most developers don’t know this:

**If the subquery returns even ONE NULL value, the entire filter behaves differently.**

Let’s imagine the `Departments` table has these rows:

![](https://miro.medium.com/v2/resize:fit:796/1*adyp2ulKzn2f87qqwVAjFw.png)

Now, guess what happens?

Your subquery becomes:

```sql
SELECT department_id  
FROM Departments  
WHERE location = 'London';
```

Expected output (in your mind):

```
10  
20
```

**But in real life, the subquery might include NULL values depending on the schema or join conditions.**

If a NULL slips in, your `IN` condition becomes:

```sql
department_id IN (10, 20, NULL)
```

Now watch closely…

If `department_id` is `10` or `20`, it works fine.  
But if you're checking for ANY value, and the subquery might contain NULL, then:

- `department_id = 10` → TRUE
- `department_id = 20` → TRUE
- `department_id = ANY OTHER VALUE` → **UNKNOWN**, not FALSE

This UNKNOWN behaves like **false** in the WHERE clause and silently filters out rows.

This is why results become unpredictable, especially when tables contain NULL values you didn’t expect.

## The Real Problem: Developers Forget SQL’s 3-Valued Logic

In programming languages like Java, Python, or C#, conditions are either:

- TRUE
- FALSE

But SQL adds a third option:

- **UNKNOWN**

This UNKNOWN comes mainly from NULL comparisons.

For example:

```
NULL = NULL       → UNKNOWN  
NULL <> NULL      → UNKNOWN  
NULL IN (NULL)    → UNKNOWN
```

So when the subquery returns a NULL value…

**Your IN condition stops behaving the way you expect.**

This is where 99% of candidates fail in interviews.

## What the Interviewer Really Wants You to Say

When they ask:

> _“What will be the output?”_

They actually want you to answer like this:

**“It depends. If the subquery returns NULL values, the comparison may result in UNKNOWN and filter out rows unexpectedly.”**

This shows:

- You understand NULL behavior
- You know SQL’s 3-valued logic
- You’re not blindly assuming

That’s senior-level thinking.

## The Right Way to Write This Query

If you want consistent and predictable results, you should rewrite the query using **EXISTS** instead of **IN**.

**Why?**  
Because `EXISTS` does not care about NULLs at all.

Here is the safer version:

```sql
SELECT e.name, e.salary  
FROM Employees e  
WHERE e.salary > 5000  
AND EXISTS (  
    SELECT 1  
    FROM Departments d  
    WHERE d.department_id = e.department_id  
    AND d.location = 'London'  
);
```

Now your query behaves exactly as expected, even if NULLs appear.

## IN vs EXISTS (Simple Explanation)

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*e6NubnK7meGcJIn_gKemGg.png)

Interviewers love when developers explain:

> _“I prefer EXISTS for subqueries because it avoids NULL issues that cause IN to behave unpredictably.”_

That’s exactly the kind of answer that gets you hired.

## Bonus: Another Common Trick Question

If the interviewer gives you this query:

```sql
SELECT *   
FROM Employees  
WHERE department_id NOT IN (SELECT department_id FROM Departments);
```

This is even worse than the previous case.

If even ONE NULL appears in the subquery, the entire filter fails.  
The result will be **zero rows**, even if data exists.

Most candidates never mention this.

You will.

## Final Thoughts

If you felt SQL was simple before, now you understand why interviewers love asking these questions.

Not to confuse you…

But to check if you truly understand how SQL thinks.

Here’s the golden rule:

> ==**_Whenever you use IN or NOT IN, always check if the subquery can return NULL.  
> If yes, switch to EXISTS or a JOIN._**==

This ONE understanding separates junior developers from confident, senior-level ones.