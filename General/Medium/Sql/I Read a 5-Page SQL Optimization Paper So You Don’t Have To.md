
 _tips. Measurable results. Zero fluff._

There’s a 2015 research paper floating around titled “Query Optimization Techniques Tips For Writing Efficient And Faster SQL Queries.” It’s five pages. It has charts. It tested every tip against Oracle’s sample Sales database and recorded actual time reductions.

Most developers will never read it.

That’s a shame, because some of these tips show 61–85% query time reductions with changes that take about 30 seconds to make.

Here’s the whole thing, distilled.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*-qzXEikvDZ6ydOQe_YerFw.png)

Ai Generated Image

## How SQL Actually Executes (The Part Most People Skip)

Before the tips, a quick mental model. When you write a SQL query, the database doesn’t just “run it.” There’s a query optimizer in the middle that builds an execution plan essentially a decision tree for how to fetch the data.

```

Your SQL Query  
      |  
      v  
Query Parser (syntax check)  
      |  
      v  
Query Optimizer  
      |-- Cost-based: estimates cheapest execution path  
      |-- Heuristic-based: applies known rules  
      |  
      v  
Execution Plan  
      |  
      v  
Data Retrieval → Result Set
```


The tips below work because they either help the optimizer make better decisions, or they remove work it doesn’t need to do in the first place.

## The 10 Tips (With the Numbers That Matter)

**Tip 1: SELECT columns, not SELECT *** _27% time reduction_

The laziest habit in SQL. When you write `SELECT *`, the database fetches every column across the network. If you only need two columns, you're paying for twenty.

``` sql

-- Slow  
SELECT * FROM sales;  
  
-- Fast  
SELECT prod_id FROM sales;
```

**Tip 2: WHERE before GROUP BY, not HAVING** _31% time reduction_

HAVING filters _after_ all rows are grouped. WHERE filters _before_. If your condition doesn’t depend on an aggregate, it belongs in WHERE.

``` sql
-- Slow: groups everything, then filters  
SELECT cust_id, COUNT(cust_id)  
FROM sales  
GROUP BY cust_id  
HAVING cust_id != '1660';  
  
-- Fast: filters first, then groups less data  
SELECT cust_id, COUNT(cust_id)  
FROM sales  
WHERE cust_id != '1660'  
GROUP BY cust_id;
```

**Tip 3: Drop unnecessary DISTINCT** _85% time reduction_

DISTINCT forces the database to sort and deduplicate the entire result set. If your primary key is already in the result, duplicates are mathematically impossible. You’re paying for work that can’t produce a result.

``` sql
-- Slow: DISTINCT is pointless here (primary key present)  
SELECT DISTINCT * FROM sales s  
JOIN customers c ON s.cust_id = c.cust_id;  
  
-- Fast  
SELECT * FROM sales s  
JOIN customers c ON s.cust_id = c.cust_id;
```

**Tip 4: Un-nest your subqueries** _61% time reduction_

Correlated subqueries run once per row. Joins run once. Rewriting a nested query as a JOIN is one of the highest-leverage changes you can make.

``` sql
-- Slow: subquery runs for every row in products  
SELECT * FROM products p  
WHERE p.prod_id = (  
    SELECT s.prod_id FROM sales s  
    WHERE s.cust_id = 100996  
);  
  
-- Fast: single join operation  
SELECT p.* FROM products p, sales s  
WHERE p.prod_id = s.prod_id  
AND s.cust_id = 100996;
```

**Tip 5: Use IN instead of multiple ORs** _73% time reduction_

The optimizer can sort an IN-list to match index order. Chained OR conditions can’t be optimized the same way.

``` sql
-- Slow  
WHERE prod_id = 14 OR prod_id = 17;  
  
-- Fast  
WHERE prod_id IN (14, 17);
``` 

**Tip 6: EXISTS over DISTINCT on one-to-many joins** _61% time reduction_

When joining tables with one-to-many relationships, DISTINCT fetches everything and deduplicates. EXISTS stops as soon as it finds a match.

``` sql
-- Slow  
SELECT DISTINCT c.country_id, c.country_name  
FROM countries c, customers e  
WHERE e.country_id = c.country_id;  
  
-- Fast: stops at first match per country  
SELECT c.country_id, c.country_name  
FROM countries c  
WHERE EXISTS (  
    SELECT 'X' FROM customers e  
    WHERE e.country_id = c.country_id  
);
```

**Tip 7: UNION ALL over UNION (when duplicates don’t matter)** _81% time reduction_

UNION scans for duplicates across the full combined result. UNION ALL skips that entirely. If your data can’t have duplicates, or you don’t care about them, UNION ALL is strictly faster.

``` sql
-- Slow  
SELECT cust_id FROM sales  
UNION  
SELECT cust_id FROM customers;  
  
-- Fast  
SELECT cust_id FROM sales  
UNION ALL  
SELECT cust_id FROM customers;
```

**Tip 8: Replace OR in JOIN conditions with UNION ALL** _70% time reduction_

OR in a join condition prevents index usage. Split it into two clean joins combined with UNION ALL instead.

``` sql
-- Slow: OR blocks index optimization  
SELECT * FROM costs c  
INNER JOIN products p  
ON c.unit_price = p.prod_min_price  
OR c.unit_price = p.prod_list_price;  
  
-- Fast: two indexed joins  
SELECT * FROM costs c  
INNER JOIN products p ON c.unit_price = p.prod_min_price  
UNION ALL  
SELECT * FROM costs c  
INNER JOIN products p ON c.unit_price = p.prod_list_price;
```

**Tip 9: No functions on indexed columns in WHERE clauses** _70% time reduction_

Wrapping a column in a function in your WHERE clause breaks index usage. The database can’t use the index on `time_id` if you're calling `EXTRACT(time_id)` — it has to compute the function for every single row first.

``` sql
-- Slow: function prevents index usage on time_id  
WHERE EXTRACT(YEAR FROM time_id) = 2001;  
  
-- Fast: BETWEEN works with the index  
WHERE time_id BETWEEN '01-JAN-2001' AND '31-DEC-2001';
``` 

**Tip 10: Pre-calculate your math** _11% time reduction (smaller, still worth it)_

If your WHERE clause has arithmetic, the database recalculates it for every row. Do the math once before the query runs.

``` sql
-- Slow: recalculates +10000 for every row  
WHERE cust_id + 10000 < 35000;  
  
-- Fast: constant is evaluated once  
WHERE cust_id < 25000;
```

## The Mental Checklist

Before you ship a query, run it through this:

```
Does it use SELECT *?         → Specify columns  
Filter in HAVING?             → Move to WHERE  
Any DISTINCT?                 → Do you actually need it?  
Nested subquery?              → Can it be a JOIN?  
OR in WHERE/JOIN?             → Try IN or UNION ALL  
Function on a column?         → Move function to value side  
Math in WHERE?                → Pre-calculate it
```

## The Honest Reality

None of this requires a new framework, a new database, or a ticket to conference. These are rewrites. Most take under five minutes. Some show time reductions above 70%.

The paper tested these on Oracle, but the principles translate to PostgreSQL, MySQL, and SQL Server the underlying query optimizer logic is similar across all of them.

The queries you wrote last year are probably still running. It might be worth checking what they’re doing.

_The original paper: “_[_Query Optimization Techniques_](https://andrewrgoss.com/pdf/sql_query_optimization_techniques.pdf)_” by Jean Habimana, IJSTR Vol. 4, Issue 10, October 2015._

[Sql](https://medium.com/tag/sql?source=post_page-----0c749649d803---------------------------------------)

[Query Optimization](https://medium.com/tag/query-optimization?source=post_page-----0c749649d803---------------------------------------)

[Research Paper](https://medium.com/tag/research-paper?source=post_page-----0c749649d803---------------------------------------)

[Database](https://medium.com/tag/database?source=post_page-----0c749649d803---------------------------------------)

[Software Engineering](https://medium.com/tag/software-engineering?source=post_page-----0c749649d803---------------------------------------)

245

1

[![The Latency Gambler](https://miro.medium.com/v2/resize:fill:96:96/1*wMFzQ6KVGegm1kaMnFxANw.jpeg)](https://medium.com/@kanishks772?source=post_page---post_author_info--0c749649d803---------------------------------------)

[## Written by The Latency Gambler](https://medium.com/@kanishks772?source=post_page---post_author_info--0c749649d803---------------------------------------)

[20K followers](https://medium.com/@kanishks772/followers?source=post_page---post_author_info--0c749649d803---------------------------------------)

·[1 following](https://medium.com/@kanishks772/following?source=post_page---post_author_info--0c749649d803---------------------------------------)

Tech critic exploring tools, systems & languages beyond hype. Linkedin-[https://www.linkedin.com/in/kanishk-singh-140059189/](https://www.linkedin.com/in/kanishk-singh-140059189/) Mail id - [kanishks772@gmail.com](mailto:kanishks772@gmail.com)

Follow