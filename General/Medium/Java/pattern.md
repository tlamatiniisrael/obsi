
# I Wasted 5 Years Writing SQL Wrong… Until I Learned This Pattern

![I Wasted 5 Years Writing SQL Wrong… Until I Learned This Pattern](https://miro.medium.com/v2/resize:fit:679/format:webp/1*g8Huf8dxelFO3BBXe-rG0w.png)

**Author**: Kavya's Programming Path  
**Published**: December 1, 2025  
**Read time**: ?

> "SELECT * FROM orders WHERE customer_id = ?"

slowly becomes:

> "SELECT o.*, c.name, p.title FROM orders o JOIN customers c ON o.customer_id = c.id JOIN products p ON o.product_id = p.id WHERE o.status = ? AND c.country = ? AND p.category IN (?, ?) ORDER BY o.created_at DESC"

And then someone duplicates it in another service.

Then someone hardcodes a filter.

Then someone adds a `LIKE '%...%'` for "search".

Bugs started coming like new episodes of a Netflix series.

## Before: Repository Hell

- You scatter raw SQL across 10 files
- Every change requires hunting down every copy
- Testing is nearly impossible
- Code reviews turn into SQL audits

## After: Query Object

Encapsulate each query in its own class:

```java
public class ActiveOrdersByCustomerQuery {
    private final Long customerId;

    public ActiveOrdersByCustomerQuery(Long customerId) {
        this.customerId = customerId;
    }

    public String getSql() {
        return "SELECT * FROM orders WHERE customer_id = ? AND status = 'ACTIVE'";
    }

    public Object[] getParams() {
        return new Object[]{customerId};
    }
}
```

Query Objects eliminate this entirely.

## 3. You keep your code clean

No need to read SQL from 7 different files.

Your business logic stays focused. Your queries become reusable, testable units.

## Final Thought

SQL isn’t the problem. **Scattered, unowned SQL is.**

Wrap it. Own it. Test it.

Your future self (and your teammates) will thank you.

---

**Thanks for reading!** If you found this useful, follow me for more practical Java insights.";