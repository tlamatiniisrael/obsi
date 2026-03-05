
I learned early on that writing a query that just runs is very different from crafting one that is easy to maintain. Over time, I also realized SQL problems are rarely unique. The same aggregations, the same joins, the same time filters kept appearing across projects. The tables and business goals differed, but the underlying patterns repeated: how many customers, which products, what is trending, and what is missing. That was the moment it clicked for me. SQL is less about inventing from scratch and more about recognizing the shape of the question and applying the right pattern.

If you write SQL regularly, you have probably seen this too. Certain problems recur across products and teams, and applying a proven pattern is the fastest path to a clean result. This guide is for backend engineers, data analysts, and product-minded developers who want practical SQL query examples, trustworthy [best practices](https://www.sqlstyle.guide/), and performance tips that hold up under real-world pressure. Each pattern includes a short explanation, a reusable syntax template, and a real-world example to show it in action. The focus is clarity and reuse, so you can turn questions into reliable queries without reinventing the wheel.

## SQL query patterns

You do not need a new trick every week. A small set of patterns covers most problems. The patterns below are those moves. They turn vague prompts into working queries that hold up in dashboards and reviews.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*STdqdFCeaXrCoHxHgXaBYA.png)

Before you write a query, start by naming the question you are answering. Most questions fall into familiar shapes, and each shape maps to a proven SQL pattern. Here are the ones you will reach for repeatedly.

- **Tally count:** If it sounds like “how many” or “how often,” go for a _tally count_. Use the [COUNT()](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_count) function to answer volume questions quickly.
- **Group bucket:** If stakeholders want ranges or segments, use the group bucket pattern. Apply [GROUP BY](https://dev.mysql.com/doc/refman/8.0/en/group-by-modifiers.html) with case expressions or ranges to create categories.
- **Rolling totals:** If the chart should show momentum rather than noise, use rolling totals. Implement with [SUM()](https://www.postgresql.org/docs/current/tutorial-window.html) over a window (ROWS BETWEEN) for cumulative trends.
- **Patch gaps:** If a report has holes, patch gaps. Join a calendar or generate a series of dates to backfill missing rows.
- **Existence check:** If the question is whether a related row exists, use an existence check. Write it with [EXISTS()](https://www.postgresql.org/docs/current/functions-subquery.html#FUNCTIONS-SUBQUERY-EXISTS) to avoid duplicates and keep the join simple.
- **Deduplication:** Use deduplication if you need one record per business key. Apply [ROW_NUMBER()](https://www.postgresql.org/docs/current/functions-window.html) or DISTINCT ON to filter to a single row per key.
- **Pattern search:** Use this to find names that match a rule​​. Depending on the requirement, use [pattern search](https://www.postgresql.org/docs/current/functions-matching.html). Reach for LIKE, regex, or full-text search.
- **Set compare:** If comparing two snapshots or sources, use set compare. Combine [UNION](https://www.postgresql.org/docs/current/typeconv-union-case.html), INTERSECT, or EXCEPT to highlight differences.
- **Join variants:** If you need a join that looks within the same table, finds missing matches, or uses ranges, explore [join variants](https://dev.mysql.com/doc/refman/8.0/en/join.html) like self-joins, anti-joins, or range joins.
- **Nested query:** If the filter depends on a single value or a list from another query, use a [nested query](https://www.postgresql.org/docs/current/functions-subquery.html). Place the logic in a WHERE … IN (subquery) or = (subquery) clause.
- **Rank and row:** If you need a top N per group, use rank and row patterns. Functions like RANK(), DENSE_RANK(), or ROW_NUMBER() make this explicit.
- **Lead-lag navigation:** If you need to compare neighboring events, use the lead-lag navigation pattern. LAG() and LEAD() functions let you peek at previous or next rows.
- **Recursive tree:** If you walk through hierarchies, use a recursive tree. Write a recursive [CTE](https://www.postgresql.org/docs/current/queries-with.html#QUERIES-WITH-CTE-MATERIALIZATION) to traverse parent-child relationships.
- **Gaps and islands:** Use gaps and islands if you need streaks or missing days. You can spot when activity streaks are broken with ROW_NUMBER() and simple date math.
- **Pivot flip:** If you must reshape results for a table or slide, use pivot flip. Functions like PIVOT, UNPIVOT, or manual aggregates transform row-column layouts.

Treat each pattern as a starting point, not a script. Swap in your columns, narrow conditions, and test on a small slice before you scale. Add comments for window frames and partition keys so you can trust the result in the future. The goal is repeatable queries that answer the question clearly and survive the next round of changes.

## Tally count

When you must answer _how_ many, _how_ often, or _what_ is most common, this is the first pattern you should look for:
```sql
SELECT    column_to_group,  
          COUNT(*) AS tally_count  
FROM      table_name  
[WHERE    condition]  
GROUP BY  column_to_group  
[ORDER BY tally_count DESC];
```
Use it to rank error codes, top referrers, or most active users. The optional WHERE focuses the window, and ORDER BY turns the results into a leaderboard.

## Group bucket

You have messy ranges or categories, and you want an agreed-upon narrative. Consider segmenting users by age group, categorizing products by price tiers, or bucketing support tickets by resolution time. This is where bucketing queries come in:
```sql
SELECT    CASE  
            WHEN condition1 THEN 'Bucket 1'  
            WHEN condition2 THEN 'Bucket 2'  
            ELSE 'Other'  
          END AS bucket_label,  
          AGG_FUNC(target_column) AS summary  
FROM      table_name  
GROUP BY  bucket_label;
```
This technique uses a CASE statement within a GROUP BY clause to dynamically assign records to predefined ranges. Good uses include customer age groups, revenue bands, and order size tiers. Keep bucket rules stable so charts do not shift week to week.

## Rolling totals

Cumulative or sliding windows are how you stop fighting volatility and start reading trends. Questions like “What is our year-to-date revenue?” or “What’s the 7-day moving average of user signups?” are impossible without cumulative calculations.
```sql
SELECT    group_key,  
          order_ts,  
          AVG(metric) OVER (  
              PARTITION BY group_key  
              ORDER BY     order_ts  
              ROWS BETWEEN 2 PRECEDING AND CURRENT ROW  
          ) AS rolling_avg  
FROM      table_name;
```
Swap AVG for SUM when you want to calculate running totals. The frame ROWS BETWEEN N PRECEDING AND CURRENT ROW controls how smooth it feels.

This rolling totals pattern trimmed runtime on a 10M+ row event log by avoiding nested subqueries.

## Patch gaps

Patch gaps have two jobs: first, they fill missing values, then fill missing rows. Time series data is powerful, but its integrity is compromised when there are gaps. A missing day in a sales report or a gap in user activity logs can lead to incorrect analysis and flawed conclusions. Identifying and filling these gaps is a crucial data-cleaning step.
```sql
-- 1) Patch NULLs  
SELECT    COALESCE(target_column, default_value) AS filled_value  
FROM      table_name;  
  
-- 2) Generate a date range, then left join  
WITH RECURSIVE date_range AS (  
  SELECT    CAST('2025-01-01' AS DATE) AS d  
  UNION ALL  
  SELECT    DATE_ADD(d, INTERVAL 1 DAY)  
  FROM      date_range  
  WHERE     d < '2025-01-31'  
)  
SELECT    r.d AS day,  
          COALESCE(a.metric, 0) AS metric  
FROM      date_range AS r  
LEFT JOIN actuals     AS a  
       ON a.day = r.d;
```
I’ve fixed dashboards where missing days made the charts misleading. Patching gaps prevents that.

## Existence check

When you only care whether a related row exists, not what it contains.
```sql
-- items that have at least one related row  
SELECT    m.*  
FROM      main_table AS m  
WHERE     EXISTS (  
           SELECT 1  
           FROM   related_table AS r  
           WHERE  r.foreign_key = m.primary_key  
         );  
  
-- items that have no related rows  
SELECT    m.*  
FROM      main_table AS m  
WHERE     NOT EXISTS (  
           SELECT 1  
           FROM   related_table AS r  
           WHERE  r.foreign_key = m.primary_key  
         );
```

Use this for customers with orders, products that were never purchased, and users without profiles. It is expressive and usually efficient.

## Deduplication

This means taking different roads to the same clean list. Choose the one that fits your rule.
```sql
-- keep exactly one row per business_key by recency  
WITH ranked AS (  
  SELECT    t.*,  
            ROW_NUMBER() OVER (  
              PARTITION BY business_key  
              ORDER BY     event_ts DESC  
            ) AS rn  
  FROM      raw_table AS t  
)  
SELECT    *  
FROM      ranked  
WHERE     rn = 1;  
  
-- remove perfect duplicates  
SELECT    DISTINCT *  
FROM      raw_table;
```

Use GROUP BY when you want an aggregate winner per key. Use ROW_NUMBER() when you need a deterministic tiebreaker like the latest timestamp.

## Pattern search

Start with LIKE, level up to REGEXP when naming rules get real. SQL provides the LIKE operator with wildcards (% and _) for simple searches, which is often sufficient and can be indexed effectively.  
For more complex scenarios, such as validating an email format or extracting a specific substring, you’ll need the power of regular expressions, supported by most modern databases through functions like REGEXP_LIKE or SIMILAR TO. While incredibly powerful, regex operations can be computationally expensive and may not use standard indexes, so they should be used judiciously. Understanding the trade-offs between LIKE, full-text search, and regex is key to building performant search features.
```sql
-- wildcard search  
SELECT    id, name  
FROM      items  
WHERE     name LIKE 'Acme%';  
  
-- regular expression search  
SELECT    id, email  
FROM      users  
WHERE     email REGEXP '^[A-Za-z0-9._%+-]+@example\\.com$';
```

Use LIKE for prefix, suffix, or contains.  
Use [REGEXP](https://dev.mysql.com/doc/refman/8.0/en/regexp.html) for formats and validation. MySQL implements regex with ICU (the Unicode-compatible regular expression engine), which is robust and Unicode-aware.

## Set compare

Answers what changed, what is missing, and what overlaps. Set operations are a cornerstone of relational algebra and are incredibly useful for comparing two datasets. SQL provides three main operators: UNION, INTERSECT, and EXCEPT.

- UNION combines the result sets of two queries and removes duplicates.
- INTERSECT returns only the rows that are present in both result sets.
- EXCEPT (or MINUS in Oracle) returns the rows from the first query that are not present in the second.
```sql
-- A minus B using anti join  
SELECT    a.*  
FROM      A AS a  
LEFT JOIN B AS b  
       ON a.id = b.id  
WHERE     b.id IS NULL;  
  
-- intersection using EXISTS  
SELECT    a.*  
FROM      A AS a  
WHERE     EXISTS (  
           SELECT 1  
           FROM   B AS b  
           WHERE  b.id = a.id  
         );  
  
-- if you are on MySQL 8.0.31+ you can also write  
-- SELECT ... FROM A  
-- INTERSECT [ALL]  
-- SELECT ... FROM B;  
-- SELECT ... FROM A  
-- EXCEPT [ALL]  
-- SELECT ... FROM B;
```

Anti-join reads like English, EXISTS scales well, and recent MySQL releases add INTERSECT and EXCEPT, which make intent obvious.  
These [set operators](https://dev.mysql.com/doc/refman/8.0/en/set-operations.html) require 8.0.31 or newer. MySQL 8.0.31+ added INTERSECT and EXCEPT, but before that, anti-joins were the practical fallback.

## Join variants

Joins are a fundamental part of SQL, allowing you to combine rows from two or more tables based on a related column. A deep understanding of the different join types is non-negotiable for anyone working with relational data.

The main types are INNER JOIN, which returns only matching rows from both tables, and the OUTER JOIN family (LEFT, RIGHT, FULL), which includes non-matching rows from one or both tables. Choosing the wrong join type can lead to incorrect results or unintentionally excluded data. Beyond equality joins, a few patterns do a lot of work.
```sql
-- self join: compare rows in the same table  
SELECT    c1.id AS child_id,  
          p.id  AS parent_id  
FROM      categories AS c1  
JOIN      categories AS p  
       ON c1.parent_id = p.id;  
  
-- anti join: rows in left without matches in right  
SELECT    l.*  
FROM      left_table  AS l  
LEFT JOIN right_table AS r  
       ON r.id = l.rid  
WHERE     r.id IS NULL;  
  
-- non-equi join: range match  
SELECT    o.id, t.tax_rate  
FROM      orders AS o  
JOIN      tax_brackets AS t  
       ON o.amount BETWEEN t.min_amount AND t.max_amount;
```

Self joins are perfect for manager relationships or referrals. Anti joins reveal missing links. Non-equi joins cover banded pricing and effective date ranges.

## Nested query

Subqueries, or queries nested inside another query, are a powerful tool for breaking down complex problems. They can be used in the SELECT, FROM, WHERE, or HAVING clauses to perform intermediate calculations, filter data based on an aggregated value, or create temporary derived tables.
```sql
-- scalar subquery  
SELECT    e.*  
FROM      employees AS e  
WHERE     e.salary = (  
           SELECT    MAX(salary)  
           FROM      employees  
         );  
  
-- set subquery  
SELECT    u.*  
FROM      users AS u  
WHERE     u.id IN (  
           SELECT    user_id  
           FROM      orders  
           WHERE     status = 'paid'  
         );
```

Scalar subqueries make one-to-one comparisons simple. IN, EXISTS, and table subqueries let you keep complex filters contained.

## Rank and row

Ranking results is a common requirement for leaderboards, top-N reports, and identifying duplicates. SQL provides a suite of window functions specifically for this: RANK(), DENSE_RANK(), and ROW_NUMBER().

- ROW_NUMBER() assigns a unique, sequential integer to each row.
- RANK() assigns the same rank to rows with the same value in the ordering column, but leaves gaps in the sequence for subsequent ranks.
- DENSE_RANK() also assigns the same rank to tied values but does not leave gaps.

Understanding the subtle differences between these functions is key to producing the correct output. For example, DENSE_RANK() is ideal for a “Top 10” list where you want to include all ties, while ROW_NUMBER() is perfect for deduplication.
```sql
SELECT    group_col,  
          item_id,  
          value,  
          RANK()       OVER (PARTITION BY group_col ORDER BY value DESC) AS rnk,  
          DENSE_RANK() OVER (PARTITION BY group_col ORDER BY value DESC) AS drnk,  
          ROW_NUMBER() OVER (PARTITION BY group_col ORDER BY value DESC) AS rn  
FROM      table_name;
```

RANK skips on ties, DENSE_RANK does not, ROW_NUMBER is unique per row. Filter on rn <= 3 to get a top 3 per group.

## Lead-lag navigation

Time series analysis often requires comparing a data point with the one immediately before or after. This is where the LEAD() and LAG() window functions are indispensable. LAG() provides access to a value from a previous row in the result set, while LEAD() accesses a subsequent row.

These functions eliminate the need for costly self-joins when performing period-over-period comparisons, such as calculating month-over-month sales growth or the time difference between consecutive user events. They simplify the query syntax and dramatically improve performance for this common class of analytical problems. It’s a pattern that directly supports building intelligent, time-aware product features.
```sql
SELECT    user_id,  
          event_ts,  
          amount,  
          LAG(amount)  OVER (PARTITION BY user_id ORDER BY event_ts) AS prev_amount,  
          LEAD(amount) OVER (PARTITION BY user_id ORDER BY event_ts) AS next_amount  
FROM      events;
```
Now you can compute deltas, detect streaks, and answer whether this action went up or down. MySQL 8 ships these [window functions](https://how.dev/answers/window-functions-in-sql).

## Recursive tree

Walk parent-child relationships to any depth.
```sql
WITH RECURSIVE tree_cte AS (  
  -- anchor  
  SELECT    id, parent_id, 1 AS lvl  
  FROM      nodes  
  WHERE     parent_id IS NULL  
  
  UNION ALL  
  
  -- recursive  
  SELECT    c.id, c.parent_id, p.lvl + 1  
  FROM      nodes   AS c  
  JOIN      tree_cte AS p  
         ON c.parent_id = p.id  
)  
SELECT    *  
FROM      tree_cte;
```
Use it for organizational charts, comment threads, and nested categories. Stop conditions live in the recursive member. Recursive queries start with a base case and then keep joining back to build the hierarchy. The query continues to execute until the recursive member returns no more rows. This powerful but advanced pattern must be used carefully to avoid infinite loops, especially with cyclical data.

## Gaps and islands

In event data, we often need to identify “islands” (continuous sequences of events) and “gaps” (breaks between those sequences). For example, you might want to find consecutive days a user was active, or identify periods of server downtime. This classic SQL puzzle can be solved elegantly with window functions. For example, finding missing days or continuous streaks without procedural code.
```sql
-- islands: consecutive days per user  
WITH sequenced AS (  
  SELECT    user_id,  
            activity_day,  
            ROW_NUMBER() OVER (  
              PARTITION BY user_id  
              ORDER BY     activity_day  
            ) AS seq  
  FROM      activity  
),  
grouped AS (  
  SELECT    user_id,  
            activity_day,  
            DATE_SUB(activity_day, INTERVAL seq DAY) AS island_key  
  FROM      sequenced  
)  
SELECT    user_id,  
          MIN(activity_day) AS island_start,  
          MAX(activity_day) AS island_end,  
          COUNT(*)          AS len_days  
FROM      grouped  
GROUP BY  user_id, island_key;
```

The trick uses [ROW_NUMBER()](https://dev.mysql.com/doc/refman/8.4/en/window-function-descriptions.html#function_row-number) to build a constant difference per island. Aggregate by that key to get streak boundaries. This approach reliably surfaced session boundaries without extra preprocessing in sessionization for event logs with tens of millions of rows.

## Pivot flip

The standard tabular format of a SQL result set (rows and columns) is not always the best format for reporting or visualization. Sometimes you need to pivot the data, turning unique values from one column into multiple new columns. For example, transforming a monthly sales table into a report where each month is its own column.

Most modern database systems have a specific PIVOT operator to handle this. The same result can be achieved for those that don’t, using conditional aggregation, a CASE statement inside an aggregate function like SUM() or MAX(). This technique is fundamental for business intelligence and creating the cross-tabular reports common in analytics dashboards.
```sql
-- rows to columns  
SELECT    category,  
          SUM(CASE WHEN month = '2025-06' THEN amount ELSE 0 END) AS m_2025_06,  
          SUM(CASE WHEN month = '2025-07' THEN amount ELSE 0 END) AS m_2025_07,  
          SUM(CASE WHEN month = '2025-08' THEN amount ELSE 0 END) AS m_2025_08  
FROM      sales  
GROUP BY  category;  
  
-- columns to rows (manual unpivot)  
SELECT    id, 'red'  AS color, red_value  AS val FROM measures  
UNION ALL  
SELECT    id, 'blue' AS color, blue_value AS val FROM measures  
UNION ALL  
SELECT    id, 'green' AS color, green_value AS val FROM measures;
```

Keep the number of pivoted columns modest so maintenance stays sensible.

## Checklist you can bookmark

This section is meant to be a quick-reference playbook. When writing a query and unsure which direction to take, use this checklist to match your question to a known SQL pattern. It’s not exhaustive, but it will cover 80% of the situations you’ll face in day-to-day analytics or backend reporting.

- Start with the question. If it sounds like how many or most common, reach for tally count.
- If stakeholders say segment, define buckets first, then aggregate.
- If a chart has holes, patch gaps with a date series plus a left join.
- If the question is did this happen, use an existence check.
- If you smell duplicates, pick a deterministic rule and use row_number.
- If you need the top N per group, use rank and row.
- For row-to-row comparisons, use lead-lag.
- For trees, use a recursive CTE.
- For before-vs-after data, use set compare.
- For reshape, use pivot flip with CASE and SUM.

## Where can you go next?

If you build analytics that other teams will reuse, formalizing these SQL patterns as named recipes in your internal documentation is the best step. Shared recipes prevent every new analyst or engineer from reinventing the wheel.

For hands-on practice and more common SQL query patterns, explore [Grokking the SQL Interview Patterns](https://www.educative.io/courses/sql-interview-patterns?utm_campaign=persona_web_dev_q3&utm_source=medium&utm_medium=text&utm_content=sumit_persona_blog_september_30&eid=5082902844932096) on Educative.

You can also learn from widely trusted references like the [MySQL 8.0 Reference Manual](https://dev.mysql.com/doc/refman/8.0/en/) and the [Mode SQL Tutorial](https://mode.com/sql-tutorial). Both offer reliable syntax guides and practical examples that complement the patterns in this guide.

You now have a toolkit of SQL query patterns you can reuse: tally count for sequences, group bucket for readable segments, rolling totals for growth views, existence checks for correctness, and pivot flips for board-ready tables. The best way to learn is to apply. Pick one pattern this week and adapt it to your own report. Small wins compound, and clear SQL becomes a shared language across your team.