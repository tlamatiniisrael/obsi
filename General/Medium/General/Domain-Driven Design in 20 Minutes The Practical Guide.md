
# Domain-Driven Design in 20 Minutes: The Practical Guide

![](https://miro.medium.com/v2/resize:fit:1400/1*xIg8QFj7xUwIdfXzIA7DPA.jpeg)

I sat in an architecture meeting last month where someone said “we need to use DDD” with the same confidence people use when recommending microservices. ==Everyone nodded. Nobody asked what that actually meant.== Later, I watched the same team create a “UserService” with 47 methods and call it Domain-Driven Design because they put the word “Domain” in a folder name.

Here’s what I’ve learned from watching architects actually apply DDD: it’s not about folder structures or naming conventions. It’s about understanding your business well enough to model it correctly — and that understanding changes everything about how you design software.

Let me break down what DDD actually is, why it matters, and how to start using it without reading a 560-page book first.

## What DDD Actually Is (And Isn’t)

Domain-Driven Design is a software design approach introduced by Eric Evans in his 2003 book of the same name. The core idea is deceptively simple: your software should reflect the business domain it serves, and the people building it should speak the same language as the people who understand the business.

That’s it. Everything else — bounded contexts, aggregates, entities, value objects — are patterns that help you achieve that goal.

What DDD is NOT:

- A folder structure convention
- A way to organize your code into layers
- Something you “add” to an existing architecture
- A silver bullet for complexity
- Only applicable to microservices

==The biggest misconception I see is teams treating DDD as a technical pattern rather than a design philosophy.== They create `Domain`, `Application`, and `Infrastructure` folders and declare victory. But if the code inside those folders doesn't reflect how the business actually works, you've just reorganized your mess into prettier boxes.

## The Two Halves of DDD

DDD has two distinct parts, and most teams only know about one of them.

## Strategic Design (The Part Most Teams Skip)

Strategic design is about the big picture: how do you break down a large, complex domain into manageable pieces? This is where concepts like Bounded Contexts and Context Maps live.

Strategic design answers questions like:

- Where are the natural boundaries in our business domain?
- Which teams should own which parts of the system?
- How do different parts of the system communicate?

## Tactical Design (The Part Everyone Focuses On)

Tactical design is about the implementation details within a single bounded context: entities, value objects, aggregates, repositories, domain events. This is the stuff that shows up in code.

Here’s the problem: tactical patterns without strategic thinking is like optimizing database queries before you’ve figured out what data you actually need. You might write beautiful code that solves the wrong problem.

I’ve watched teams spend weeks debating whether something should be an Entity or a Value Object while completely ignoring the fact that they’ve drawn their service boundaries in the wrong place. As I discussed in [Why Most Microservices Fail](https://medium.com/@thedevsuite/why-most-microservices-fail-and-how-to-avoid-the-same-mistakes-2dd8809ee423), getting boundaries wrong is one of the most common failure modes — and DDD’s strategic design is specifically meant to help you avoid that trap. The strategic decisions matter more.

## Ubiquitous Language: The Foundation

If you take only one thing from DDD, make it this: Ubiquitous Language.

Ubiquitous Language means that everyone — developers, product managers, domain experts, QA — uses the same terms to describe the same concepts. And those terms should appear in your code exactly as they appear in business conversations.

Eric Evans emphasizes that using this shared language in conversations with domain experts is essential for testing and refining your model. As he writes: “By using the model-based language pervasively and not being satisfied until it flows, we approach a model that is complete and comprehensible, made up of simple elements that combine to express complex ideas.”

Here’s what this looks like in practice:

**Without Ubiquitous Language:**

```
// Developer-speak that means nothing to the business  
class DataProcessor {  
    void processRecord(Record r) {  
        if (r.status == 1) {  
            r.status = 2;  
            updateTimestamp(r);  
        }  
    }  
}
```

**With Ubiquitous Language:**

```
// Business-speak that everyone understands  
class LoanApplication {  
    void approve() {  
        if (this.status == ApplicationStatus.PENDING_REVIEW) {  
            this.status = ApplicationStatus.APPROVED;  
            this.approvalDate = LocalDate.now();  
        }  
    }  
}
```

The second version isn’t just more readable — it’s a shared artifact that developers and business people can discuss. When a product manager says “what happens when we approve a loan application?”, a developer can point to this code and have a meaningful conversation.

The discipline required is harder than it sounds. Every time you’re tempted to use a generic term like “process,” “handle,” or “manage,” stop and ask: what does the business call this? Use that word instead.

## Bounded Contexts: Drawing the Lines

A Bounded Context is a boundary within which a particular model is defined and applicable. Inside that boundary, terms have specific, consistent meanings. Outside that boundary, the same terms might mean something completely different.

Fowler illustrates this with a story from his early career at an electricity utility: “The word ‘meter’ meant subtly different things to different parts of the organization: was it the connection between the grid and a location, the grid and a customer, the physical meter itself (which could be replaced if faulty).”

In conversation, people smooth over these differences. In code, you can’t. A `Customer` in your Sales context might have completely different attributes and behaviors than a `Customer` in your Support context. Trying to create one unified `Customer` model that serves both contexts leads to a bloated, confusing mess.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*N_VGqcAT0OKzRvmqShUxwQ.png)

Each context has its own model of `Customer` and `Product` — and that's not duplication, it's appropriate separation. The Sales context doesn't need to know about shipping dimensions. The Billing context doesn't care about delivery preferences.

## How to Identify Bounded Contexts

This is more art than science, but here are patterns I’ve observed architects use:

1. **Listen for language changes.** When different teams or departments use the same word to mean different things, that’s a boundary.
2. **Look for organizational boundaries.** Teams that work independently often represent natural bounded contexts. Conway’s Law applies here.
3. **Identify core vs. supporting domains.** Your core business differentiator deserves its own context with careful modeling. Generic capabilities (authentication, notifications) can be separate contexts with simpler models.
4. **Watch for model tension.** If you’re constantly fighting your model — adding flags, special cases, or “type” fields to handle different scenarios — you might have multiple contexts crammed into one.

## Tactical Patterns: The Building Blocks

Once you’ve identified your bounded contexts, tactical patterns help you model the domain within each context.

## Entities

Entities are objects with a distinct identity that persists over time. Two entities with the same attributes are still different if they have different identities.

```
// An Order is an Entity - it has a unique identity  
public class Order {  
    private final OrderId id;  // Identity  
    private OrderStatus status;  
    private List<OrderLine> lines;  
    private Money total;  
      
    // The identity matters - two orders with the same   
    // items are still different orders  
}
```

## Value Objects

Value Objects are defined by their attributes, not their identity. Two value objects with the same attributes are interchangeable.

```
// Money is a Value Object - defined entirely by its attributes  
public class Money {  
    private final BigDecimal amount;  
    private final Currency currency;  
      
    // Two Money objects with the same amount and currency  
    // are equal and interchangeable  
      
    public Money add(Money other) {  
        if (!this.currency.equals(other.currency)) {  
            throw new CurrencyMismatchException();  
        }  
        return new Money(this.amount.add(other.amount), this.currency);  
    }  
}
```

Value Objects should be immutable. Operations on them return new instances rather than modifying the existing one. This makes them easier to reason about and safer to share.

## Aggregates

An Aggregate is a cluster of entities and value objects that are treated as a single unit for data changes. Every aggregate has a root entity (the Aggregate Root) that controls access to everything inside.

This is where I see teams struggle most. The temptation is to make aggregates too large — to include everything that’s “related.” But aggregates should be as small as possible while still maintaining consistency.

``` java
// Order is an Aggregate Root  
// OrderLines exist only within the context of an Order  
public class Order {  
    private final OrderId id;  
    private List<OrderLine> lines;  // Part of the aggregate  
    private CustomerId customerId;   // Reference to another aggregate, not the object itself  
      
    public void addLine(Product product, int quantity) {  
        // Business rules enforced here  
        if (this.status != OrderStatus.DRAFT) {  
            throw new OrderNotModifiableException();  
        }  
        this.lines.add(new OrderLine(product.getId(), quantity, product.getPrice()));  
        recalculateTotal();  
    }  
}
```

Key rules for aggregates:

- **Reference other aggregates by ID, not by object.** An Order holds a `CustomerId`, not a `Customer` object.
- **Transactions should not span aggregates.** If you need to update two aggregates atomically, you might have drawn the boundary wrong.
- **Keep them small.** Large aggregates create contention and performance problems.

## Domain Events

Domain Events capture something that happened in the domain that other parts of the system might care about. They’re named in past tense because they represent facts that have already occurred.

```java
public class OrderPlaced {  
    private final OrderId orderId;  
    private final CustomerId customerId;  
    private final Money total;  
    private final Instant occurredAt;  
      
    // Immutable - this is a fact about what happened  
}
```

Domain events enable loose coupling between bounded contexts. The Order context publishes `OrderPlaced`; the Shipping context subscribes and creates a shipment; the Billing context subscribes and generates an invoice. Each context reacts independently.

## A Practical Example: Modeling a Library System

Let me walk through how DDD thinking changes the design of a simple library system.

**Naive approach (what most teams do):**

```
- Book (id, title, author, isbn, available, borrowedBy, dueDate)  
- User (id, name, email, borrowedBooks, fines)  
- Transaction (id, bookId, userId, type, date)
```

This creates a tangled mess where `Book` knows about borrowing, `User` knows about fines, and everything is connected to everything.

**DDD approach:**

First, identify the bounded contexts:

- **Catalog Context:** Managing the library’s collection
- **Lending Context:** Managing the borrowing process
- **Membership Context:** Managing library members

Each context has its own model:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*2o1TsiT4FM_lrF6ArVotSg.png)

Notice how `Book` in the Catalog context is different from `LoanableItem` in the Lending context. The Catalog cares about metadata, location, and condition. The Lending context only cares that something can be borrowed — it doesn't need to know the publication year.

The Lending context might look like this:

```java
// Aggregate Root  
public class Loan {  
    private final LoanId id;  
    private final ItemId itemId;  
    private final MemberId memberId;  
    private LocalDate dueDate;  
    private LocalDate returnDate;  
    private LoanStatus status;  
      
    public void extend(int days) {  
        if (this.status != LoanStatus.ACTIVE) {  
            throw new LoanNotExtendableException();  
        }  
        if (this.hasBeenExtended()) {  
            throw new MaxExtensionsReachedException();  
        }  
        this.dueDate = this.dueDate.plusDays(days);  
        this.recordExtension();  
    }  
      
    public void markReturned() {  
        this.returnDate = LocalDate.now();  
        this.status = LoanStatus.RETURNED;  
          
        if (this.returnDate.isAfter(this.dueDate)) {  
            // Publish event for Membership context to handle  
            DomainEvents.publish(new LoanReturnedLate(  
                this.id,   
                this.memberId,   
                this.dueDate,   
                this.returnDate  
            ));  
        }  
    }  
}
```

The Membership context subscribes to `LoanReturnedLate` and creates a fine. The contexts are decoupled — the Lending context doesn't know how fines work, it just reports what happened.

## Common Mistakes (And How to Avoid Them)

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*XwL7vb5vaaS7zGaZ2L7Fbw.jpeg)

## Mistake 1: Anemic Domain Models

An anemic domain model has entities that are just data containers — all the business logic lives in services. This is the opposite of what DDD intends.

```java
// Anemic - the entity is just a data bag  
public class Order {  
    private OrderId id;  
    private OrderStatus status;  
    private List<OrderLine> lines;  
      
    // Just getters and setters, no behavior  
}


public class OrderService {  
    public void addLine(Order order, Product product, int quantity) {  
        // All logic lives here instead of in the entity  
        if (order.getStatus() != OrderStatus.DRAFT) {  
            throw new OrderNotModifiableException();  
        }  
        order.getLines().add(new OrderLine(...));  
    }  
}
```

```java
// Rich domain model - behavior lives with the data  
public class Order {  
    private OrderId id;  
    private OrderStatus status;  
    private List<OrderLine> lines;  
      
    public void addLine(Product product, int quantity) {  
        // Logic lives where the data is  
        if (this.status != OrderStatus.DRAFT) {  
            throw new OrderNotModifiableException();  
        }  
        this.lines.add(new OrderLine(product.getId(), quantity, product.getPrice()));  
        this.recalculateTotal();  
    }  
}
```

## Mistake 2: One Model to Rule Them All

Trying to create a single unified model across the entire system leads to bloated, confusing entities that try to serve everyone and serve no one well.

Accept that different contexts need different models. A `Product` in your Inventory context is not the same as a `Product` in your Pricing context. That's okay.

## Mistake 3: Starting with Tactical Patterns

Teams often jump straight to “should this be an Entity or Value Object?” before understanding their domain boundaries. Strategic design should come first.

Spend time with domain experts. Draw context maps. Understand the language. Then worry about aggregates.

## Mistake 4: Ignoring the Ubiquitous Language

If your code uses different terms than your business people, you’ve already failed at DDD. Every time you translate between “business speak” and “developer speak,” you introduce opportunities for misunderstanding.

## Getting Started: A Practical Path

If you’re new to DDD, here’s how I’d suggest approaching it:

1. **Start with language.** Before writing any code, spend time with domain experts. Learn their vocabulary. Create a glossary. Use their terms in your code.
2. **Identify one bounded context.** Don’t try to model your entire system. Pick one area — ideally your core business differentiator — and focus there.
3. **Model the domain on paper first.** Sketch out the entities, their relationships, and the key behaviors. Event Storming — a collaborative workshop technique developed by Alberto Brandolini where teams map out domain events on sticky notes — is particularly effective for discovering bounded contexts and understanding how the business actually works.
4. **Keep aggregates small.** When in doubt, make them smaller. You can always combine later; splitting is harder.
5. **Let the model evolve.** Your first model will be wrong. That’s fine. DDD is about continuous refinement as you learn more about the domain.

## Key Takeaways

- **DDD is a design philosophy, not a folder structure.** It’s about modeling your software to reflect the business domain, using a shared language that everyone understands.
- **Strategic design matters more than tactical patterns.** Getting your bounded contexts right is more important than debating Entity vs. Value Object.
- **Ubiquitous Language is the foundation.** If developers and business people use different words for the same concepts, you’re not doing DDD.
- **Bounded Contexts let the same term mean different things.** A `Customer` in Sales is different from a `Customer` in Support, and that's okay.
- **Keep aggregates small and focused.** They’re consistency boundaries, not containers for everything related.

## Related Articles :

1. [Monolith vs Microservices: A Practical Decision Framework](https://blog.thedevsuite.com/monolith-vs-microservices-a-practical-decision-framework-a9aef12174c5)
2. [Why Most Microservices Fail (And How to Avoid the Same Mistakes)](https://blog.thedevsuite.com/why-most-microservices-fail-and-how-to-avoid-the-same-mistakes-2dd8809ee423)

## References

1. Evans, E. _Domain-Driven Design: Tackling Complexity in the Heart of Software_. Addison-Wesley, 2003.
2. Fowler, M. “BoundedContext.” [_martinfowler.com_](http://martinfowler.com/), January 2014. [https://martinfowler.com/bliki/BoundedContext.html](https://martinfowler.com/bliki/BoundedContext.html)
3. Fowler, M. “UbiquitousLanguage.” [_martinfowler.com_](http://martinfowler.com/), December 2006. [https://martinfowler.com/bliki/UbiquitousLanguage.html](https://martinfowler.com/bliki/UbiquitousLanguage.html)
4. Vernon, V. _Implementing Domain-Driven Design_. Addison-Wesley, 2013.
5. Khononov, V. _Learning Domain-Driven Design: Aligning Software Architecture and Business Strategy_. O’Reilly Media, 2021.
6. Microsoft Azure Architecture Center. “Using tactical DDD to design microservices.” _Microsoft Learn_, 2024. [https://learn.microsoft.com/en-us/azure/architecture/microservices/model/tactical-ddd](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/tactical-ddd)
7. Brandolini, A. “Introducing Event Storming.” [_eventstorming.com_](http://eventstorming.com/), 2013. [https://www.eventstorming.com/](https://www.eventstorming.com/)

_Until next time, keep observing, keep learning._

**— The Architect’s Notebook**

_I’m a Software Engineer learning architecture by watching architects work. If these field notes help you understand architecture better, consider following for more observations every Week._

**What’s your experience with DDD?** Have you tried applying it to a real project? I’d love to hear what worked and what didn’t — especially the parts that were harder than expected. The best insights come from comparing notes.