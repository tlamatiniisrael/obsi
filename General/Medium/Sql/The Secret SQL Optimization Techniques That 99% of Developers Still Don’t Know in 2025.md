
_And why your database is probably crying right now_

![](https://miro.medium.com/v2/resize:fit:1226/1*S9fzsLBfZO6JjDl6j32abA.jpeg)

[https://imgflip.com/i/a9c61j](https://imgflip.com/i/a9c61j)

Today, I’m spilling the tea on advanced SQL optimization techniques that most developers haven’t discovered yet. These aren’t your typical “add an index” suggestions — we’re diving deep into the rabbit hole.

> [**Access not Member**](https://medium.com/@nandoseptian/the-secret-sql-optimization-techniques-that-99-of-developers-still-dont-know-in-2025-2c1a219a1766?sk=92006f9e1159cfa6e6ab8adb0b94ff21)

## The Problem: Your Queries Are Probably Terrible

Let me guess — you’ve optimized your queries by adding a few indexes, maybe used `EXPLAIN` once or twice, and called it a day? That's cute. Meanwhile, your database is processing millions of rows when it should be touching hundreds.

Here’s the harsh truth: most developers stop learning SQL optimization after the basics. They master JOINs, understand indexing fundamentals, and think they’re database wizards. Spoiler alert: you’re not.

## 1. Window Functions vs Subqueries: The Performance Gap Nobody Talks About

Everyone knows about window functions, but few understand their true optimization potential. Consider this common pattern:

```sql
-- The "I learned SQL yesterday" approach  
SELECT user_id, order_date, order_amount,  
       (SELECT AVG(order_amount)   
        FROM orders o2   
        WHERE o2.user_id = o1.user_id) as avg_user_orders  
FROM orders o1;
```

This query runs a subquery for EVERY row. With 1M orders, that’s potentially 1M subqueries. Your database server just filed a complaint with HR.

Here’s the optimized version using window functions:

```sql
-- The "I actually understand SQL" approach  
SELECT user_id, order_date, order_amount,  
       AVG(order_amount) OVER (PARTITION BY user_id) as avg_user_orders  
FROM orders;
```

Performance difference: Up to 10x faster on large datasets. But here’s the kicker — most developers don’t realize you can combine multiple window functions with different frames:

```sql
SELECT user_id, order_date, order_amount,  
       AVG(order_amount) OVER (PARTITION BY user_id) as user_avg,  
       AVG(order_amount) OVER (PARTITION BY user_id   
                               ORDER BY order_date   
                               ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) as rolling_avg_3  
FROM orders;
```

This calculates both lifetime average AND rolling 3-order average in a single pass. Mind = blown.

## 2. The Art of Lateral Joins (PostgreSQL’s Best Kept Secret)

If you’re still using correlated subqueries in PostgreSQL, we need to talk. `LATERAL` joins are like regular joins but with superpowers — they can reference columns from preceding tables in the FROM clause.

```sql
-- Old school (slow)  
SELECT c.customer_name,  
       (SELECT product_name   
        FROM orders o   
        JOIN order_items oi ON o.order_id = oi.order_id  
        JOIN products p ON oi.product_id = p.product_id  
        WHERE o.customer_id = c.customer_id  
        ORDER BY o.order_date DESC  
        LIMIT 3) as recent_products  
FROM customers c;
```

```sql
-- New school (fast)  
SELECT c.customer_name, recent.product_names  
FROM customers c  
CROSS JOIN LATERAL (  
    SELECT string_agg(p.product_name, ', ') as product_names  
    FROM orders o  
    JOIN order_items oi ON o.order_id = oi.order_id  
    JOIN products p ON oi.product_id = p.product_id  
    WHERE o.customer_id = c.customer_id  
    ORDER BY o.order_date DESC  
    LIMIT 3  
) recent;
```

The `LATERAL` version processes each customer once and efficiently fetches their recent products. It's like having a personal assistant for each row.

## 3. Recursive CTEs: Not Just for Org Charts Anymore

Common Table Expressions (CTEs) are mainstream, but recursive CTEs? That’s where things get spicy. Beyond the typical hierarchical data scenarios, they’re perfect for complex data transformations.

Here’s a mind-bending example — generating a running balance with transaction dependencies:

```sql
WITH RECURSIVE running_balance AS (  
    -- Base case: first transaction  
    SELECT transaction_id, account_id, amount, date,  
           amount as balance,  
           1 as level  
    FROM transactions   
    WHERE transaction_id = (  
        SELECT MIN(transaction_id)   
        FROM transactions   
        WHERE account_id = 12345  
    )  
      
    UNION ALL  
      
    -- Recursive case  
    SELECT t.transaction_id, t.account_id, t.amount, t.date,  
           rb.balance + t.amount as balance,  
           rb.level + 1  
    FROM transactions t  
    JOIN running_balance rb ON t.account_id = rb.account_id  
    WHERE t.date > rb.date  
    AND t.account_id = 12345  
)  
SELECT * FROM running_balance ORDER BY date;
```

This calculates running balances without cursors or multiple passes. It’s recursion doing the heavy lifting.

## 4. Index-Only Scans: The Covering Index Hack

Here’s something that’ll make you rethink your indexing strategy. Most developers create indexes on WHERE clause columns and call it good. But what if your query never touched the actual table?

Now the same query becomes an index-only scan. The database never touches the heap table — everything comes from the index. It’s like having a cheat sheet for every test.

## 5. The FILTER Clause: Conditional Aggregates Done Right

Want to blow your colleagues’ minds? Show them this technique for conditional aggregates:

-- The amateur hour approach  
SELECT   
    product_id,  
    SUM(CASE WHEN order_date >= '2025-01-01' THEN quantity ELSE 0 END) as qty_2025,  
    SUM(CASE WHEN order_date >= '2024-01-01' AND order_date < '2025-01-01' THEN quantity ELSE 0 END) as qty_2024,  
    COUNT(CASE WHEN status = 'completed' THEN 1 END) as completed_orders  
FROM order_items oi  
JOIN orders o ON oi.order_id = o.order_id  
GROUP BY product_id;

-- The elegant approach  
SELECT   
    product_id,  
    SUM(quantity) FILTER (WHERE order_date >= '2025-01-01') as qty_2025,  
    SUM(quantity) FILTER (WHERE order_date >= '2024-01-01' AND order_date < '2025-01-01') as qty_2024,  
    COUNT(*) FILTER (WHERE status = 'completed') as completed_orders  
FROM order_items oi  
JOIN orders o ON oi.order_id = o.order_id  
GROUP BY product_id;

The `FILTER` clause is cleaner, more readable, and often performs better than `CASE WHEN` constructs.

## 6. Materialized Views with Smart Refresh Strategies

Materialized views aren’t new, but most developers use them wrong. They either refresh too often (killing performance) or too rarely (serving stale data).

Here’s a hybrid approach using triggers for smart refreshes:

-- Create materialized view  
CREATE MATERIALIZED VIEW daily_sales_summary AS  
SELECT   
    date_trunc('day', order_date) as sale_date,  
    COUNT(*) as order_count,  
    SUM(total_amount) as total_sales,  
    AVG(total_amount) as avg_order_value  
FROM orders   
WHERE order_date >= CURRENT_DATE - INTERVAL '30 days'  
GROUP BY date_trunc('day', order_date);  
  
-- Smart refresh trigger  
CREATE OR REPLACE FUNCTION refresh_daily_sales()  
RETURNS TRIGGER AS $$  
BEGIN  
    -- Only refresh if the change affects recent data  
    IF NEW.order_date >= CURRENT_DATE - INTERVAL '30 days' OR  
       OLD.order_date >= CURRENT_DATE - INTERVAL '30 days' THEN  
        REFRESH MATERIALIZED VIEW CONCURRENTLY daily_sales_summary;  
    END IF;  
    RETURN NULL;  
END;  
$$ LANGUAGE plpgsql;

This refreshes the materialized view only when relevant data changes, not on every single transaction.

## 7. The DISTINCT ON Technique (PostgreSQL’s Hidden Gem)

Need the latest record per group? Most developers reach for window functions, but PostgreSQL has a cleaner solution:

-- The window function way (more complex)  
SELECT user_id, login_time, ip_address  
FROM (  
    SELECT user_id, login_time, ip_address,  
           ROW_NUMBER() OVER (PARTITION BY user_id ORDER BY login_time DESC) as rn  
    FROM user_sessions  
) ranked  
WHERE rn = 1;

-- The DISTINCT ON way (elegant)  
SELECT DISTINCT ON (user_id) user_id, login_time, ip_address  
FROM user_sessions  
ORDER BY user_id, login_time DESC;

`DISTINCT ON` returns the first row for each distinct value in the specified columns. It's simpler and often faster than window functions for this specific use case.

## The Reality Check: Why These Techniques Matter in 2025

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*LNwfQHozkYX81VYCSB-viQ.jpeg)

Photo by [Medienstürmer](https://unsplash.com/@medienstuermer?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/two-people-sitting-during-day-aWf7mjwwJJo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

As data volumes explode and real-time requirements become standard, these optimizations aren’t just “nice to have” — they’re survival skills. A 10x query performance improvement can mean the difference between a snappy application and one that times out.

## Putting It All Together: A Real-World Example

Here’s how these techniques combine in a real analytics query:

WITH RECURSIVE user_hierarchy AS (  
    SELECT user_id, manager_id, 1 as level  
    FROM employees WHERE manager_id IS NULL  
    UNION ALL  
    SELECT e.user_id, e.manager_id, uh.level + 1  
    FROM employees e  
    JOIN user_hierarchy uh ON e.manager_id = uh.user_id  
),  
sales_metrics AS (  
    SELECT DISTINCT ON (s.user_id, date_trunc('month', s.sale_date))  
           s.user_id,  
           date_trunc('month', s.sale_date) as month,  
           SUM(s.amount) OVER (PARTITION BY s.user_id, date_trunc('month', s.sale_date)) as monthly_sales,  
           COUNT(*) FILTER (WHERE s.status = 'completed') OVER (PARTITION BY s.user_id, date_trunc('month', s.sale_date)) as completed_sales  
    FROM sales s  
    WHERE s.sale_date >= CURRENT_DATE - INTERVAL '12 months'  
    ORDER BY s.user_id, date_trunc('month', s.sale_date), s.sale_date DESC  
)  
SELECT   
    uh.user_id,  
    uh.level as hierarchy_level,  
    sm.month,  
    sm.monthly_sales,  
    sm.completed_sales,  
    LAG(sm.monthly_sales) OVER (PARTITION BY uh.user_id ORDER BY sm.month) as prev_month_sales  
FROM user_hierarchy uh  
LEFT JOIN LATERAL (  
    SELECT * FROM sales_metrics sm2   
    WHERE sm2.user_id = uh.user_id  
    ORDER BY sm2.month DESC  
    LIMIT 12  
) sm ON true  
ORDER BY uh.level, uh.user_id, sm.month;

This query combines recursive CTEs, DISTINCT ON, window functions, FILTER clauses, and LATERAL joins. It’s a symphony of advanced SQL techniques working together.

## Conclusion

Most developers plateau at intermediate SQL skills and wonder why their applications don’t scale. These advanced techniques separate the senior developers from the rest. They’re not just about performance — they’re about thinking differently about data problems.

Start implementing these techniques gradually. Your database will thank you, your users will notice the speed improvement, and your career will benefit from the deep technical knowledge.

Remember: In a world of ORMs and query builders, raw SQL optimization skills are your competitive advantage.

## References

- [https://www.pingcap.com/article/window-functions-vs-subqueries-sql-comparative-analysis/](https://www.pingcap.com/article/window-functions-vs-subqueries-sql-comparative-analysis/)
- [https://www.cybertec-postgresql.com/en/understanding-lateral-joins-in-postgresql/](https://www.cybertec-postgresql.com/en/understanding-lateral-joins-in-postgresql/)
- [https://blog.replaybird.com/postgresql-lateral-join/](https://blog.replaybird.com/postgresql-lateral-join/)