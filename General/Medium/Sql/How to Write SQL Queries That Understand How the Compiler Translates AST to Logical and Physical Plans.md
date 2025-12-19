
## Reading SQL the Way the Engine Sees It, So You Can Predict Performance and Optimize with Intent

![](https://miro.medium.com/v2/resize:fit:4896/1*35R8Egfg0RoGqklczP2uaA.png)

Image by [Dave Connors](https://docs.getdbt.com/blog/authors/dave-connors)

**_— Non Member_**_: Pls take a look_ [**_here_**](https://medium.com/@Rohan_Dutt/7969e3d7b1ee?sk=3bff892bc149e2f595165ea6e44971b4)**!**

Early SQL days felt like debugging blind. Write query. Hit Enter.

Hope the planets line up. _Click. Wait. Pray._

Then the rude awakening. A tiny join locked up the system. Not because the logic was wrong but because the engine silently reshaped my query into something unrecognizable.

Modern engines are ruthless. They fuse operators, reorder filters, prune branches, reassign join types, and rewrite entire AST subtrees before you ever see a row.

Most developers never notice because the SQL looks clean while the compiler rearranges the furniture in the dark.

`EXPLAIN` was the moment everything snapped into focus. That one keyword exposed my “simple” query as a pipeline of scans, filters, hash builds, projections, and runtime shortcuts. That is when you realize the truth: databases do not execute your query. They restructure it.

Take this innocent-looking query:

```sql
SELECT c.id, SUM(t.amount)  
FROM customers c  
JOIN transactions t ON c.id = t.customer_id  
WHERE t.event_ts >= CURRENT_DATE - INTERVAL '90 days'  
GROUP BY c.id;
```

The engine often turns it into something closer to:

```
Aggregate  
  Hash Join  
    Filter (event_ts pushed down)  
      Scan transactions  
    Projection (id only, pruned columns)  
      Scan customers
```      

And during execution the engine may switch join strategies, fuse the aggregation into the join pipeline, or batch filters across vectors. The plan you imagine rarely matches the plan the engine chooses.

Another quiet rewrite:

```sql
SELECT *  
FROM sales  
WHERE region IN ('APAC', 'EU')  
AND amount > 500;
```

Under the hood:

- `IN` becomes a bitmap filter
- Predicates jump to the earliest possible scan boundary
- Unused columns get pruned
- Entire blocks get skipped through metadata

The AST gets chopped into nodes. The logical planner applies rewrite rules. The physical planner maps each node to a real operator. And adaptive logic adjusts the plan if the data misbehaves.

This is why slow queries are rarely about the SQL text. They are usually about how the compiler reshaped your tree.

Your clarity appears when you read the plan:

```sql
EXPLAIN ANALYZE  
SELECT user_id, COUNT(*)  
FROM events  
WHERE event_type = 'CLICK'  
AND created_at > CURRENT_DATE - INTERVAL '1 day'  
GROUP BY user_id;
```

Look at:

- predicate placement
- join order
- column pruning
- pipeline structure
- operator costs

That is the real language of the engine.

Lightbulb moments that change how you write SQL:

- Understanding your AST is not the final shape of your query
- Seeing how logical planning removes everything unnecessary
- Watching physical operators choose the fastest path
- Realizing pushdown and pruning beat hand-tuned hacks

## Cracking the Logical Plan Like a Pro 🛠️💡

I remember staring at my first “perfect” SQL query.  
All the joins lined up.

WHERE clauses tidy. Hit run. Damn.

Thirty seconds later still spinning.  
Turns out I was writing beautiful SQL that engines secretly despised.

The fix was simple.  
Understand how the compiler turns your text into an AST.  
Understand how that AST becomes a logical plan.  
Understand how the logical plan becomes a physical execution path.

Filter pushdowns became my obsession.  
Why scan a mountain of rows when you can discard junk at the door.  
I learned that the hard way when unnecessary scans inflated my query cost for no real reason.

Then came the nightmare report.  
Ten minutes to load.  
I changed one predicate location. Boom. Two seconds.  
That was the moment everything clicked.

Engines are not smart.  
They are obedient.  
They will run exactly what you wrote even if it is the slowest path possible.

That is when I stopped writing SQL like a sentence.  
I started writing it like directions for the optimizer.

Scan this first.  
Filter here.  
Join that after.

And if the optimizer tries something different, I check the plan and guide it back on track.  
No more guessing. No more silent detours.  
Just raw predictable speed. 🚀

Here is where understanding the logical plan truly matters.

Example query you think is fine:

```sql
SELECT u.id, o.amount  
FROM users u  
JOIN orders o ON u.id = o.user_id  
WHERE o.amount > 500;
```

What the logical planner often prefers:

```sql
SELECT u.id, o.amount  
FROM (  
    SELECT user_id, amount  
    FROM orders  
    WHERE amount > 500  
) o  
JOIN users u ON u.id = o.user_id;
```

Filter early.  
Project only what matters.  
Join later. Cleaner pipeline. Faster path.

Another example that burns beginners:

```sql
SELECT *  
FROM events  
WHERE event_type = 'CLICK'  
AND created_at >= CURRENT_DATE - INTERVAL '1 day';
```

The compiler almost always rewrites this into a plan that pushes both predicates into the scan node.  
The logical plan strips columns you never asked for.  
The physical plan may switch strategies if row patterns change.

Reading the plan is not optional anymore.  
It is survival.

## **Mastering the Physical Plan for Maximum Speed⚡**

PostgreSQL taught me a brutal lesson. My “simple” join query crawled for minutes.

The physical plan revealed the truth.

It had chosen a nested loop join. Total disaster for large tables. Hash join would have crushed it.

I finally understood that join type is not a detail.  
It is the entire performance story.

Here is the kind of mess I ran into:

```sql
SELECT a.id, b.value  
FROM table_a a  
JOIN table_b b ON a.id = b.a_id;
```

The engine sometimes picks a nested loop even when it should not.  
The fix is forcing the right join path through better predicates and tighter filters.  
Not shortcuts.  
Just guiding the optimizer.

Then the next surprise hit me.  
Spark-style hinting felt like raw power.  
I used a hash join hint.

The query time dropped from minutes to seconds.  
Instant adrenaline. Not magic.

Just choosing an operator that matches the data shape.

Then another twist. MySQL taught me pain. I added an index expecting speed.  
Instead the query slowed down.  
I almost threw my laptop.

Turns out broad filters give indexes no useful selectivity.  
The engine did extra work scanning unnecessary rows. Indexes are not a universal fix.

They are tools that only shine under the right conditions.

Sometimes you actually need to force the index path.  
Sometimes you need to avoid it entirely.

Knowing the physical plan is the only way to decide.

Here is a classic trap:

```sql
SELECT *  
FROM logs  
WHERE created_at >= CURRENT_DATE - INTERVAL '7 days';
```

If the date range is huge, the index is useless.  
The engine will flip to a full scan.  
If the range is tight, the index becomes your best friend.  
Physical plans tell you which one is happening.

Then I discovered `EXPLAIN ANALYZE`. Holy crap!!

It felt like putting an X-ray over the execution.  
Every node. Every cost. Every misstep exposed.  
  
There is nothing more honest than a runtime plan.

I even unlocked massive gains with parallel workers.

Turning on parallelism felt like removing a speed limiter.  
Not cheating. Just letting the engine breathe.

## When the Compiler Shows Its Actual Hand 🎯

Your SQL runs blind until you force the engine to reveal what it plans to do.  
Most queries fail not in syntax but in execution.  
  
Plans tell the truth. SQL text lies sometimes.

Run an execution plan on your slowest query and stare at the first mistake  
the engine makes.

```sql
EXPLAIN ANALYZE  
SELECT o.order_id, c.country  
FROM orders o  
JOIN customers c ON o.customer_id = c.id  
WHERE c.country = 'USA';
```

- Look for a sequential scan when an index exists.
- Look for a nested loop where a hash join should appear.
- Look for rows exploding before filtering.

One bad operator is usually the root of the slowdown. Finding that one node cracks open the entire problem.

### Reshaping the Logical Plan To Take Control

The AST is only the beginning.

- Logical planner decides filter order.
- The logical planner decides join sequencing.
- The logical planner decides column pruning.
- That single step determines almost all performance outcomes.

Most beginners filter too late.  
Most slow queries come from making the engine sift through trash before throwing it away.

Here is a pattern that kills performance:

```sql
SELECT *  
FROM orders o  
JOIN customers c ON o.customer_id = c.id  
WHERE c.region = 'APAC' AND o.total > 1000;
```

Here is the form the logical planner prefers:

```sql
WITH filtered_orders AS (  
    SELECT order_id, customer_id, total  
    FROM orders  
    WHERE total > 1000  
)  
SELECT f.order_id, f.total, c.region  
FROM filtered_orders f  
JOIN customers c ON f.customer_id = c.id  
WHERE c.region = 'APAC';
```

Filter early. Join later.  
Shorter pipelines always win.  
When filters shrink data first, logical plans get smaller and physical plans get smarter.

### Steering the Physical Plan For Actual Speed

The physical planner chooses real operators.  
That is where cost, rows, algorithms, and memory pressure collide.  
Sometimes the default is perfect.  
Sometimes it is a disaster.

Here is a join the engine might pick poorly:

```sql
SELECT a.id, b.value  
FROM table_a a  
JOIN table_b b ON a.id = b.a_id;
```

For large tables the engine might still fall back to a nested loop.  
That is catastrophic.  
Forcing a hash join or blocking a nested loop can completely flip performance.

```sql
SET enable_nestloop = off;  
  
EXPLAIN ANALYZE  
SELECT a.id, b.value  
FROM table_a a  
JOIN table_b b ON a.id = b.a_id;
```

The difference is obvious.  
Rows handled drop.  
Buffers required drop.  
Execution time drops.

Physical operators matter more than SQL syntax.  
Operators decide everything.

### Hunting Hidden Bottlenecks Inside the Engine

- Some queries break because the planner guesses wrong.
- Some break because statistics are stale.
- Some break because indexes exist but cannot be used.
- Some break because the column order sabotages predicate selectivity.

To see how the engine understands your data, inspect its statistics:

```sql
SELECT *  
FROM pg_stats  
WHERE tablename = 'customers';
```

If the stats lie, the plan lies.  
Fix the stats.

```sql
ANALYZE customers;
```

If the engine chooses the wrong index path, you can guide it.

```sql
SELECT *  
FROM customers  
WHERE country = 'USA'  
AND status = 'ACTIVE';
```

If the combined filter creates high selectivity, the index becomes golden.  
If the selectivity is broad, the index becomes poison.  
Plans reveal which outcome you are living with.

### Building Planner Intuition That Makes Queries Predictable

Take three real production queries.  
Run execution plans on all three.  
Find the first confusing operator in each plan.  
Rewrite one query by thinking the way the compiler restructures the AST.  
Re-run the plan.  
Measure buffers, row counts, and actual timing.

Here is a more advanced workload example to practice with:

```sql
EXPLAIN ANALYZE  
SELECT d.department_id,  
       SUM(s.amount) AS total_sales,  
       COUNT(*) AS txn_count  
FROM departments d  
JOIN stores s     ON s.department_id = d.department_id  
JOIN transactions t ON t.store_id = s.store_id  
WHERE t.txn_date >= CURRENT_DATE - INTERVAL '90 days'  
AND d.active = true  
GROUP BY d.department_id  
HAVING SUM(s.amount) > 500000  
ORDER BY total_sales DESC;
```

- Look at where the filters hit.
- Look at how many rows explode before grouping.
- Look at how join order changes your world.

Understanding AST to logical plan to physical plan is the real unlock. Once you understand the compiler, SQL stops surprising you.

Performance becomes predictable. Optimization becomes intentional.

## Thankyou… Clap 50 times and Follow for more :)