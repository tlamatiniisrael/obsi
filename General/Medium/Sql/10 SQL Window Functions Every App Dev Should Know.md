
## Practical, production-ready patterns for pagination, deduping, time-series trends, and “show me my rank” features — without shipping data to your app layer.

![](https://miro.medium.com/v2/resize:fit:1400/1*OPZNzArYr8INAhpT71Carw.png)

_Learn the 10 most useful SQL window functions for app developers — with patterns for pagination, deduping, running totals, and time-series analysis._

You don’t need a data warehouse badge to use window functions. You just need one stubborn UI request — “show my rank among friends,” “paginate deterministically,” “trend this metric” — and suddenly the _OVER()_ clause becomes your best friend. Let’s be real: pushing this logic to the app layer is slower, jankier, and harder to maintain. SQL already knows how to do it, beautifully.

Below are the 10 window functions I reach for on real products, with opinionated examples that run on PostgreSQL, MySQL 8+, and most modern engines.

## A Tiny Working Data Model

Assume an `orders` table:

```sql
CREATE TABLE orders (  
  id            BIGSERIAL PRIMARY KEY,  
  user_id       BIGINT NOT NULL,  
  created_at    TIMESTAMPTZ NOT NULL,  
  amount_cents  INTEGER NOT NULL,  
  status        TEXT CHECK (status IN ('pending','paid','refunded'))  
);
```

We’ll reference this table throughout. When you see `PARTITION BY user_id`, think “per user.” When you see `ORDER BY created_at`, think “time order.”

> _Tip: For consistent results, ensure the_ `_ORDER BY_` _column(s) are unique or tie-broken (e.g.,_ `_ORDER BY created_at, id_`_)._

## 1) `ROW_NUMBER()` — Deterministic Pagination & De-duplication

**When to use:** numbered rows within a group; remove duplicates but keep the “best” row.

```sql
SELECT *  
FROM (  
  SELECT o.*,  
         ROW_NUMBER() OVER (  
           PARTITION BY user_id  
           ORDER BY created_at DESC, id DESC  
         ) AS rn  
  FROM orders o  
) t  
WHERE rn <= 10; -- last 10 orders per user
```

**Why it’s great:** app-friendly pagination that doesn’t drift when new rows arrive (tie-break by `id`). For de-dupe, swap the filter: `WHERE rn = 1`.

## 2) `RANK()` — Leaderboards With Gaps

**When to use:** public leaderboards where ties share the same rank and gaps appear (1,1,3…).

```sql
SELECT user_id,  
       SUM(amount_cents) AS revenue,  
       RANK() OVER (ORDER BY SUM(amount_cents) DESC) AS revenue_rank  
FROM orders  
WHERE status = 'paid'  
GROUP BY user_id;
```

**Why it’s great:** matches users’ mental model of “shared rank = tied.”

## 3) `DENSE_RANK()` — Leaderboards Without Gaps

**When to use:** marketing dashboards that want 1,1,2 (no gaps).

DENSE_RANK() OVER (ORDER BY SUM(amount_cents) DESC) AS revenue_dense_rank

**Pro tip:** Use `DENSE_RANK` when you plan to prize “Top 3” and don’t want fewer than three winners due to ties.

## 4) `NTILE(n)` — Bucketing Users Into Percentiles/Quartiles

**When to use:** segment users into `n` equal groups (e.g., top 25%).

SELECT user_id,  
       SUM(amount_cents) AS revenue,  
       NTILE(4) OVER (ORDER BY SUM(amount_cents)) AS quartile  
FROM orders  
GROUP BY user_id;

**UI pattern:** “You’re in the top quartile of spenders this month.”

## 5) `LAG()` — Compare to Previous Row (Churn & Deltas)

**When to use:** compute day-over-day, event-to-event differences.

WITH daily AS (  
  SELECT date_trunc('day', created_at) AS day,  
         SUM(amount_cents) AS revenue  
  FROM orders  
  WHERE status = 'paid'  
  GROUP BY 1  
)  
SELECT day,  
       revenue,  
       LAG(revenue) OVER (ORDER BY day) AS prev_revenue,  
       revenue - LAG(revenue) OVER (ORDER BY day) AS delta  
FROM daily  
ORDER BY day;

**Why it’s great:** fuels sparkline deltas like “+12% vs yesterday” without self-joins.

## 6) `LEAD()` — Peek Ahead (SLA Windows, Upcoming Events)

**When to use:** compute the next event’s timestamp or range.

SELECT id,  
       created_at,  
       LEAD(created_at) OVER (PARTITION BY user_id ORDER BY created_at) AS next_order_at  
FROM orders  
WHERE user_id = $1;

**Product hook:** “Users typically reorder within X days.”

## 7) `FIRST_VALUE()` — Pin the Baseline

**When to use:** compare each row to the first value in the partition.

```sql
SELECT user_id, created_at, amount_cents,  
       FIRST_VALUE(amount_cents) OVER (  
         PARTITION BY user_id  
         ORDER BY created_at  
       ) AS first_amount  
FROM orders;
```

**Pattern:** show “price changed since first purchase.”

## 8) `LAST_VALUE()` — Track the Latest (with a proper frame)

**The gotcha:** by default many engines’ window frame for `ORDER BY` is  
`RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`, so `LAST_VALUE` equals the current row unless you extend the frame.

```sql
SELECT user_id, created_at, amount_cents,  
       LAST_VALUE(amount_cents) OVER (  
         PARTITION BY user_id  
         ORDER BY created_at  
         ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING  
       ) AS latest_amount  
FROM orders;
```

**Use case:** show each row alongside “latest known value.”

## 9) `SUM()` OVER — Running Totals & Cumulative Funnels

**When to use:** cumulative revenue, on-boarding progress, or quota burn.

```sql
SELECT created_at::date AS day,  
       SUM(amount_cents) AS revenue,  
       SUM(SUM(amount_cents)) OVER (  
         ORDER BY created_at::date  
         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW  
       ) AS cumulative_revenue  
FROM orders  
WHERE status = 'paid'  
GROUP BY day  
ORDER BY day;
```

**Why it’s great:** a single pass yields both daily and cumulative series for charts.

## 10) `AVG()` OVER — Moving Averages That Don’t Lie

**When to use:** smooth volatile time series in-database (7-day moving average).

```sql
WITH daily AS (  
  SELECT date_trunc('day', created_at) AS day,  
         SUM(amount_cents) AS revenue  
  FROM orders  
  WHERE status = 'paid'  
  GROUP BY 1  
)  
SELECT day,  
       revenue,  
       AVG(revenue) OVER (  
         ORDER BY day  
         ROWS BETWEEN 6 PRECEDING AND CURRENT ROW  
       ) AS ma7  
FROM daily  
ORDER BY day;
```

**Frame choice matters:** `ROWS BETWEEN 6 PRECEDING AND CURRENT ROW` ensures exactly 7 points—clean math and predictable edges.

## Frames, Partitions, and Performance: A Fast Mental Model

- **PARTITION BY** = “per group.” Think of it as resetting counters.
- **ORDER BY** (inside `OVER`) = “timeline within the group.”
- **FRAME** = “which neighbors are visible to this calculation.”
- _Default_ is often `RANGE UNBOUNDED PRECEDING` to `CURRENT ROW`, which is value-based and can surprise you; most app analytics prefer **ROWS** frames for exact counts.

**Indexing:**

- Add indexes that match your window’s `PARTITION BY` and `ORDER BY` to reduce sort/scan cost, e.g., `CREATE INDEX ON orders (user_id, created_at, id);`
- For time windows, clustered storage (or at least an index on `created_at`) helps a lot.

**Cardinality sanity:**

- If each partition is huge (e.g., all rows per global user), consider bucketing by time: `PARTITION BY user_id, date_trunc('month', created_at)` when appropriate.

## Real-World Patterns You’ll Reuse

## Deterministic infinite scroll

Use `ROW_NUMBER()` to precompute page positions and keep your app’s cursor trivial:

WITH ranked AS (  
  SELECT id,  
         ROW_NUMBER() OVER (ORDER BY created_at DESC, id DESC) AS rn  
  FROM orders WHERE user_id = $1  
)  
SELECT *  
FROM ranked  
WHERE rn > $cursor  
ORDER BY rn  
LIMIT 25;

## “Show my percentile” UX

Combine `NTILE(100)` with copy like “You’re in the 92nd percentile of spenders this quarter.”

## Revenue burndown with grace

`SUM() OVER` for cumulative, `LAG()` for delta, `AVG() OVER` for MA7—all in one query, shipped straight to your chart.

## Gotchas (so you don’t learn them at 2 a.m.)

1. **Non-unique ordering → flicker.** Always tie-break your `ORDER BY` with a unique column.
2. `**LAST_VALUE**` **frame trap.** Specify `ROWS UNBOUNDED FOLLOWING` if you truly need “last.”
3. **Big partitions, small RAM.** If a partition can’t fit in memory, consider chunking with time buckets or doing incremental materializations.
4. **Engine quirks.** MySQL’s default collation/order can produce surprising results on text; normalize or cast as needed.
5. **Window vs. group confusion.** Window functions _do not reduce rows_. They annotate. When you need one row per group, use `GROUP BY` first, then window on top of that result.

## Testing & Monitoring in Production

- **Check determinism:** run the same window query twice under concurrent inserts — confirm stable pagination and identical ranks.
- **Benchmark frames:** try `ROWS` vs `RANGE` and measure. On time series, `ROWS` is often faster and more predictable.
- **Materialize when needed:** if a dashboard runs the same expensive window often, consider a nightly job to materialize intermediate aggregates and window on top of those.

## Wrap-Up

Window functions are the Swiss Army knife of app SQL. They let you rank, compare, smooth, and paginate directly in the database — cleanly, predictably, and fast. Start with `ROW_NUMBER`, `RANK/DENSE_RANK`, `NTILE`, `LAG/LEAD`, `FIRST_VALUE/LAST_VALUE`, and `SUM/AVG OVER`. Then combine them like Lego for richer UX without dragging millions of rows into your app.

**Found this useful?** Follow for hands-on backend patterns and drop a comment with the window trick you use the most — I’ll include the best ones (with credit) in a follow-up.