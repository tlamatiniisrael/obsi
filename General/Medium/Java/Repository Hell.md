
# Repository Hell: Why I Finally Escaped to JPA Criteria

![Repository Hell: Why I Finally Escaped to JPA Criteria](https://miro.medium.com/v2/resize:fit:940/1*ZZNA5mw72ZqJWFb4p0AY4g.png)

**Author**: Devrim Ozcay | Javarevisited  
**Published**: November 6, 2025  
**Read time**: 3 min

You start with a clean Spring Data repository interface:

```java
interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByEmail(String email);
}
```

Then the business team says:

> “Can we also search by name?”

Fine.

```java
List<User> findByName(String name);
```

Then:

> “What if I want both email and name?”

Okay.

```java
List<User> findByEmailAndName(String email, String name);
```

Then:

> “Also by department, role, status, and active flag… and make them all optional.”

Suddenly, your repository looks like this:

- You end up with dozens of micro-methods that are almost the same
- You write 6 different variations for the same query with optional filters
- You can’t sort or paginate cleanly
- Spring Data method names start looking like auto-generated passwords.

Worse:

- **You lose control over the actual SQL being generated.**

Sound familiar?

This is “Repository Hell” — and I escaped it using **JPA Criteria**.

## Why Criteria?

JPA Criteria is a programmatic, type-safe way to build dynamic queries. Instead of writing endless `findByXAndYOrZ` methods, you build queries on the fly.

Key benefits:

- ? Fully dynamic query construction
- ? Type-safe predicates (no raw SQL strings)
- ? Clean, testable, and reusable logic

## Step 1: Define a Filter DTO

```java
public class OrderFilter {
    private String customerEmail;
    private Status status;
    private LocalDateTime startDate;
    private LocalDateTime endDate;
    // getters/setters
}
```

## Step 2: Build a Specification

```java
public static Specification<Order> withFilter(OrderFilter filter) {
    return (root, query, cb) -> {
        List<Predicate> predicates = new ArrayList<>();

        if (filter.getCustomerEmail() != null) {
            predicates.add(cb.equal(root.get(\"customerEmail\"), filter.getCustomerEmail()));
        }
        if (filter.getStatus() != null) {
            predicates.add(cb.equal(root.get(\"status\"), filter.getStatus()));
        }
        if (filter.getStartDate() != null) {
            predicates.add(cb.greaterThanOrEqualTo(root.get(\"createdAt\"), filter.getStartDate()));
        }
        if (filter.getEndDate() != null) {
            predicates.add(cb.lessThanOrEqualTo(root.get(\"createdAt\"), filter.getEndDate()));
        }

        return cb.and(predicates.toArray(new Predicate[0]));
    };
}
```

## Step 3: Use It in Your Service

```java
List<Order> orders = orderRepository.findAll(withFilter(filter));
```

Readable. Testable. Flexible.

## Extract Reusable Specifications

You can modularize common conditions:

```java
public static Specification<Order> hasStatus(Status status) {
    return (root, query, cb) -> cb.equal(root.get(\"status\"), status);
}

public static Specification<Order> byCustomerEmail(String email) {
    return (root, query, cb) -> cb.equal(root.get(\"customerEmail\"), email);
}
```

And use them like:

```java
Specification<Order> spec = hasStatus(Status.COMPLETED)
    .and(byCustomerEmail(\"user@example.com\"));
```

## “But it’s harder to test.”

Actually — it’s easier. You can unit-test each `Specification` in isolation without a database.

## Real Production Win

We replaced 27 repository methods with **3 reusable specifications**. Query logic became predictable, debuggable, and composable.

**Criteria is your escape hatch.**

You’ll write more code, yes. But you’ll gain **full control**, **type safety**, and **long-term maintainability**.

Stop writing repositories. Start building queries.

---

**Thanks for reading!** If you found this useful, follow me for more practical Java/Spring insights.