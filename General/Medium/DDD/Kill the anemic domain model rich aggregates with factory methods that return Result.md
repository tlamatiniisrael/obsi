
# Kill the anemic domain model: rich aggregates with factory methods that return Result

Public setters plus logic-in-services is not a domain model, it is a database row with extra steps. Here is the three-rung entity hierarchy in MMCA.Common that makes invalid state unconstructable._

_Part of the MMCA.Common series · Article 5 of 41. One pattern at a time._

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*8QCb5qpzp93Fuqo9fdZgeA.png)

You have seen this class. You have probably written it. Maybe this week:

``` csharp

public class Order  
{  
    public int Id { get; set; }  
    public OrderStatus Status { get; set; }  
    public decimal Total { get; set; }  
    public List<OrderLine> Lines { get; set; } = new();  
}  
  
public class OrderService  
{  
    public void Ship(Order order)  
    {  
        if (order.Status != OrderStatus.Paid)  
            throw new InvalidOperationException("Cannot ship an unpaid order");  
        order.Status = OrderStatus.Shipped;  
    }  
}
```

The `Order` is a bag of public setters. It knows nothing and protects nothing. Anyone can set `Status = Shipped` directly and skip the check. Anyone can set `Total` to a negative number. The rule about not shipping an unpaid order lives in a _service_, off to the side, where it is one of many such services that all reach into the order and mutate it from outside.

Martin Fowler named this the anemic domain model, and the name is precise: the objects have data but no behavior. The behavior has been drained out into a procedural layer. You get all the ceremony of object orientation (classes, properties, a “domain” folder) with none of its protection. The invariants are not enforced by the type; they are enforced by everyone remembering to call the right service in the right order.

## Why it matters

The cost is not theoretical. When the rules live outside the object:

- **Invalid state is representable.** You can construct an `Order` with a negative total and no lines, because the only thing standing between you and that object is a property setter. Bugs become "how did this row get into this state" tickets that nobody can reproduce.
- **The rules drift.** The shipping check lives in `OrderService.Ship`. Six months later someone adds `BulkOrderService.ShipAll` and forgets the check, because nothing tied the rule to the object.
- **You cannot trust the object you are holding.** Every method that receives an `Order` has to re-validate, because the type guarantees nothing.

A rich domain model inverts this. The object owns its rules. The only way to get an `Order` is through a path that has already validated it, and the only way to change it is through a method that enforces the invariant. If you are holding an `Order`, it is valid, by construction.

## The MMCA answer: a three-rung hierarchy, one capability per rung

MMCA.Common builds every entity on a three-class inheritance chain. Read it bottom-up; each rung adds exactly one capability.

```csharp
BaseEntity<TId>                    // identity  
   -> AuditableBaseEntity<TId>     // + soft-delete, audit fields, optimistic concurrency  
      -> AuditableAggregateRootEntity<TId>   // + domain events, child-collection helpers
```

`**BaseEntity<TId>**` is almost nothing: a single `required init TId Id` with a `where TId : notnull` constraint. The `required init` is the load-bearing choice. A factory method sets `Id` once at construction, and `init` makes it immutable thereafter, while EF Core still materializes existing rows through the parameterless constructor and assigns `Id` via the same `init` accessor. One identity, set once, never reassigned, on both the application path and the persistence path.

`**AuditableBaseEntity<TId>**` adds the cross-cutting facts every persisted row needs. Soft-delete (`IsDeleted`, plus a `Delete()` / `Undelete()` pair that return `Result` and refuse to double-delete), audit fields (`CreatedOn/By`, `LastModifiedOn/By`) with _private_ setters, and a `RowVersion` optimistic-concurrency token. The domain never writes the audit fields; they are stamped centrally by `ApplicationDbContext.SaveChangesAsync` via change-tracker reflection. Three concerns that would otherwise be copy-pasted into every entity are inherited once and enforced in one place.

`**AuditableAggregateRootEntity<TId>**` is the top rung, the one that earns the DDD name "aggregate root." It owns a private domain-event list (`AddDomainEvent` / `ClearDomainEvents` / a read-only `DomainEvents` view), and it adds two helpers that let a root police its own consistency boundary: `SetItems<T>` (replace a child collection, routed through an overridable `ValidateSetItems` hook so a root can veto removing, say, a shipped order line) and `GetChildOrNotFound<T>` (find an active child by id or return an `Error.NotFound` failure). Only aggregate roots raise domain events, which is how the persistence layer knows where to look.

## The factory method that returns Result

Here is the heart of it. The constructor is private. The only public way in is a static `Create` that returns `Result<T>`:

``` csharp
public sealed class Order : AuditableAggregateRootEntity<OrderIdentifierType>  
{  
    private readonly List<OrderLine> _lines = [];  
    public OrderStatus Status { get; private set; }  
    public Money Total { get; private set; }  
  
    private Order() { }   // EF materialization only  
  
    public static Result<Order> Create(CustomerIdentifierType customerId, Money total)  
    {  
        var validation = Result.Combine(  
            CommonInvariants.EnsureIdIsNotDefault(customerId, nameof(customerId)),  
            EnsureTotalIsNonNegative(total));  
  
        if (validation.IsFailure)  
        {  
            return Result.Failure<Order>(validation.Errors);  
        }  
  
        return Result.Success(new Order { Id = default, Total = total, Status = OrderStatus.Pending });  
    }  
  
    public Result Ship()  
    {  
        if (Status != OrderStatus.Paid)  
        {  
            return Error.Invariant("Order.NotPaid", "Cannot ship an unpaid order");  
        }  
  
        Status = OrderStatus.Shipped;  
        AddDomainEvent(new OrderShipped(Id));  
        return Result.Success();  
    }  
}
```

Compare this to the anemic version. There are no public setters: `Status` and `Total` have `private set`. There is no service holding the shipping rule; `Ship()` _is_ the rule, and it lives on the object. You cannot `new Order(...)` from outside, so an invalid order cannot exist. The `Result.Combine` reports _every_ broken invariant at once (covered in the Result railway article), and `Ship()` raises an `OrderShipped` domain event as a first-class outcome rather than a side effect the caller might forget. Invalid state is, quite literally, unconstructable.

## Value objects: the same trick, one level down

The aggregate is built from value objects that play by the same rules. `ValueObject` is the cheapest possible base, `public abstract record ValueObject;`, so every value object inherits structural equality and immutability from `record` for free. Two `Money(10, USD)` are equal because their values match, not because they are the same row.

Each concrete value object (`Email`, `Money`, `Address`, `DateRange`) uses the same private-constructor plus static `Create` returning `Result<T>` idiom, so an invalid `Email` simply cannot be constructed. The validation lives in static _invariants_ classes (`EmailInvariants`, `AddressInvariants`) that also publish the `MaxLength` constants EF configurations and FluentValidation validators reuse, giving the field-length rules one source of truth.

## Identifier aliases: the war on primitive obsession

There is one more piece that is easy to miss but does heavy lifting against bugs. Look back at the factory signature: `Create(CustomerIdentifierType customerId, Money total)`, not `Create(int customerId, decimal total)`.

MMCA.Common defines per-entity identifier aliases, for example a solution-wide `global using UserIdentifierType = int;` linked into every project via `Directory.Build.props`. The underlying type is still `int`, but the name carries meaning. A method that takes a `UserIdentifierType` and an `OrderIdentifierType` cannot have its arguments transposed by accident in a way the compiler waves through, the way two bare `int`s can. The id is also strongly named at the entity level (`BaseEntity<TId>` is generic over the alias), and `Money` instead of `decimal` means the currency travels with the amount. This is primitive obsession addressed at the type level rather than in code review.

## Soft-delete, not destruction

Entities are never hard-deleted. `Delete()` flips `IsDeleted` to `true` and an EF global query filter hides the row; the data and its foreign-key relationships stay intact. `Delete()` returns `Result` and guards against double-deletion (returning `Error.AlreadyDeleted`), so even "remove this" flows through the same error railway as everything else.

## Trade-offs, honestly

Rich aggregates are the right default, but they are not free, and the scorecard’s §4 review names the gaps:

- **More boilerplate per entity.** A private constructor, a static factory, private setters, and invariant checks are more code than four public auto-properties. The protection is worth it, but the first entity feels heavier than the anemic version.
- **EF Core needs the parameterless constructor.** The materialization path is a real constraint: EF builds the object through `private Order()` and sets `Id` via `init`, which is why the constructor exists at all. You are designing around two construction paths, the factory and the ORM, and that costs a little ceremony.
- **Strategic DDD is downstream work, not framework work.** The tactical conventions are now machine-enforced: an `AggregateConventionTests` fitness function, merge-gated, pins that every aggregate root exposes a static `Create` returning `Result<T>` and has no public constructors, so "private constructor plus a `Create` factory" is an executable check, not a convention held by review. What no base class can give you is the strategic half of DDD: bounded contexts and a ubiquitous language are realized in the apps that build modules on these classes, not in the framework itself, and the scorecard's §4 names that as the remaining gap.
- **Aggregate boundaries are a judgment call.** Deciding what belongs inside a root and what is its own aggregate is genuine modeling work that no base class makes for you.

## Apply this even without MMCA

You do not need this framework to drop the anemic model. The moves are portable:

1. **Make constructors private and add a static factory** that validates and returns a result type. If the only public way to build an object runs through validation, an invalid object cannot exist.
2. **Replace public setters with behavior methods.** `order.Ship()`, not `order.Status = Shipped`. The rule lives on the object that owns the data.
3. **Use strong identifier types,** even if they are thin wrappers over `int` or `Guid`. Transposed-id bugs are silent and expensive; the type system can catch them for free.
4. **Push validation into value objects.** An `Email` that cannot be constructed invalid means every method downstream can stop re-checking it.

The rule of thumb: **if you are holding the object, it should be valid, by construction, and the only way to change it should be a method that keeps it valid.**

**What we covered:** why the anemic model drains protection out of your types, the `BaseEntity` to `AuditableBaseEntity` to `AuditableAggregateRootEntity` hierarchy (identity, then audit/soft-delete, then domain events), the private-constructor-plus-`Create`-returning-`Result` idiom that makes invalidity unconstructable, value objects and identifier aliases against primitive obsession, and domain events raised on the aggregate root.

**Next in the series:** specifications over LINQ spaghetti, the composable, reusable query intent that keeps read logic out of your controllers.

_MMCA.Common is MIT-licensed and open source. Star the repo, skim the domain building-blocks chapter, or_ `_dotnet add package MMCA.Common.API_` _and try it._

- Repo: [https://github.com/ivanball/MMCA.Common](https://github.com/ivanball/MMCA.Common)
- Onboarding chapter: `Docs/Onboarding/group-02-domain-building-blocks.md`.

Domain Driven Design

Software Architecture

C Sharp Programming

Dotnet

Best Practices

1

[

![Ivan Ball-llovera](https://miro.medium.com/v2/resize:fill:96:96/0*jP5q95j-f0XnbsR3)



](https://medium.com/@ivanball76?source=---post_author_info--44f2e3d89794---------------------------------------)

[

## Written by Ivan Ball-llovera

](https://medium.com/@ivanball76?source=---post_author_info--44f2e3d89794---------------------------------------)

[4 followers](https://medium.com/@ivanball76/followers?source=---post_author_info--44f2e3d89794---------------------------------------)

·[1 following](https://medium.com/@ivanball76/following?source=---post_author_info--44f2e3d89794---------------------------------------)

Follow