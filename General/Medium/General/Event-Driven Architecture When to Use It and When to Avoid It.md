
# Event-Driven Architecture: When to Use It and When to Avoid It

I was sitting in an architecture review when our lead architect drew a simple diagram on the whiteboard: boxes connected by arrows. “These services don’t call each other,” she explained. “They just announce what happened and move on.”

Someone asked the obvious question: “But how do we know anything actually worked?”

She smiled. “That’s exactly the right question to ask.”

That moment crystallized something I’d been struggling to understand about event-driven architecture. It’s not just a technical pattern — it’s a fundamentally different way of thinking about how systems communicate. And like most powerful tools, it can either solve your problems elegantly or create new ones you never anticipated.

Let me share what I’ve learned from watching teams succeed and fail with EDA.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*jxHLUYVx1ww83sWngc_pCw.jpeg)

## What “Event-Driven” Actually Means

Here’s the thing that confused me for months: when people say “event-driven architecture,” they often mean completely different things.

Martin Fowler, after a ThoughtWorks summit dedicated to untangling this confusion, identified four distinct patterns that all get lumped under the EDA umbrella:

1. **Event Notification** — A system announces that something happened, and doesn’t particularly care who’s listening or what they do about it.
2. **Event-Carried State Transfer** — Events carry enough data that consumers can update their own copies without calling back to the source.
3. **Event Sourcing** — The event log becomes the source of truth, and current state is derived by replaying events.
4. **CQRS** — Separate models for reading and writing data, often (but not always) combined with events.

These patterns solve different problems and have different trade-offs. Conflating them is a recipe for confusion — and I’ve seen teams implement “event-driven architecture” without being clear about which pattern they actually needed.

For this article, I’m focusing primarily on event notification and event-carried state transfer — the patterns most teams encounter when they first adopt EDA.

## The Core Idea: Fire and Forget

In traditional request-response architecture, Service A calls Service B and waits for an answer. It’s like making a phone call — you dial, wait for someone to pick up, have your conversation, and hang up. Simple, synchronous, and familiar.

Event-driven architecture flips this model. Service A announces “something happened” by publishing an event, then immediately moves on to its next task. It doesn’t wait. It doesn’t care who’s listening. It’s more like posting on a bulletin board — you put up your notice and walk away.

![](https://miro.medium.com/v2/resize:fit:956/1*e1TMrL55gEQp9DSv9nFKcA.png)

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*X5Vuf2h-vX2TbiHpK_zvXg.png)

This simple shift has profound implications for how systems behave under load, how they handle failures, and how they evolve over time.

## When Event-Driven Architecture Shines

## 1. Decoupling Services That Don’t Need to Know About Each Other

Imagine an e-commerce system. When an order is placed, several things need to happen:

- Inventory needs to be updated
- A confirmation email needs to be sent
- Analytics needs to record the sale
- The recommendation engine needs to update its models

In a request-response world, the Order service needs to know about all these downstream services. Every time you add a new consumer of order data, you modify the Order service. The coupling grows over time.

With events, the Order service just publishes an “OrderPlaced” event. It doesn’t know or care who’s listening. The Inventory service subscribes. The Email service subscribes. Analytics subscribes. When you add a new recommendation engine, you just add another subscriber — the Order service never changes.

As Fowler notes, “Event notification is nice because it implies a low level of coupling, and is pretty simple to set up.”

## 2. Handling Spiky, Unpredictable Load

Request-response systems are vulnerable to traffic spikes. If your Order service suddenly gets 10x normal traffic, it hammers every downstream service with 10x the requests. If any of those services can’t handle the load, the whole chain backs up.

Events with a message broker act as a buffer. The Order service publishes events as fast as it can. The broker queues them. Downstream services consume at whatever rate they can handle. The spike gets smoothed out over time.

This is why event-driven patterns are popular in systems that handle unpredictable bursts — flash sales, viral content, real-time gaming, financial trading.

## 3. Building Resilient Systems

In request-response, if Service B is down, Service A’s request fails. You need retry logic, circuit breakers, fallback behaviors — and even then, the user might see an error.

With events, if Service B is down, the events just queue up in the broker. When Service B comes back online, it processes the backlog. Service A never knew there was a problem. The user experience remains smooth.

This resilience comes with a trade-off (eventual consistency, which we’ll discuss), but for many use cases, it’s exactly what you want.

## 4. Enabling Independent Team Velocity

When services communicate through events with well-defined schemas, teams can work independently. The Order team doesn’t need to coordinate with the Inventory team for every change. They agree on the event contract, and then each team can deploy on their own schedule.

This organizational benefit is often underestimated. In large organizations, coordination overhead can be the biggest drag on velocity. Events reduce that overhead.

## When Event-Driven Architecture Hurts

## 1. When You Need Immediate Consistency

Here’s the fundamental trade-off: events are asynchronous, which means eventual consistency.

When a user places an order, they expect to see it in their order history immediately. If the Order service publishes an event and the Order History service hasn’t processed it yet, the user sees stale data. “I just placed an order, but it’s not showing up!”

For operations where users expect immediate feedback — authentication, search results, pricing checks, account balances — request-response is usually the right choice. The user is waiting; you need a synchronous answer.

As one practitioner put it: “Pick request-driven when the user waits, and correctness is immediate. Pick event-driven when work can run in the background.”

## 2. When You Need to Debug Complex Flows

Fowler warns about this trap explicitly: “It can become problematic if there really is a logical flow that runs over various event notifications. The problem is that it can be hard to see such a flow as it’s not explicit in any program text.”

In request-response, you can trace a request through your system. You see the call stack. You can step through with a debugger. The flow is explicit in the code.

With events, the flow is implicit. Event A triggers Handler B, which publishes Event C, which triggers Handler D. The only way to see this flow is through distributed tracing and careful log correlation. When something goes wrong at 3 AM, you’re piecing together a puzzle from scattered breadcrumbs.

I’ve watched teams build beautifully decoupled event-driven systems, only to realize months later that nobody could explain how a customer order actually flowed through the system. The decoupling that felt like freedom became a debugging nightmare.

## 3. When Your Team Lacks Operational Maturity

Event-driven systems require serious operational investment:

- **Distributed tracing** to follow events across services
- **Dead letter queues** to handle events that can’t be processed
- **Monitoring** for consumer lag, queue depth, processing latency
- **Alerting** that actually tells you when something’s wrong
- **Replay capabilities** for when you need to reprocess events

If you’re still doing manual deployments and your monitoring is an afterthought, events will bury you. The complexity isn’t in writing the code — it’s in operating the system.

## 4. When Events Become Passive-Aggressive Commands

Fowler identifies a subtle anti-pattern: “A simple example of this trap is when an event is used as a passive-aggressive command. This happens when the source system expects the recipient to carry out an action, and ought to use a command message to show that intention, but styles the message as an event instead.”

An event says “this happened.” A command says “do this.”

If your “OrderPlaced” event really means “Inventory service, you must decrement stock now,” you’re using events wrong. You’ve created a hidden dependency disguised as loose coupling. When the Inventory service doesn’t behave as expected, you’ll be confused because the contract was never explicit.

Be honest about your intentions. If you need something to happen, send a command. If you’re just announcing what happened, send an event.

## 5. When You’re Building a Simple System

Not every system needs event-driven architecture. If you have a handful of services with straightforward interactions, request-response is simpler, easier to understand, and easier to debug.

I’ve seen teams adopt EDA because it’s “modern” or “scalable,” then spend months wrestling with complexity they didn’t need. A well-designed request-response system can handle significant scale. Don’t add architectural complexity until you have a genuine problem to solve.

## The Patterns That Make EDA Work

If you do adopt event-driven architecture, these patterns will save you pain:

## Idempotent Consumers

Events can be delivered more than once. Network glitches, consumer restarts, broker retries — duplicates happen. Your consumers must handle this gracefully.

def handle_order_placed(event):  
    # Check if we've already processed this event  
    if already_processed(event.id):  
        return  # Idempotent: safe to ignore duplicate  
      
    # Process the event  
    update_inventory(event.order_id, event.items)  
      
    # Mark as processed  
    mark_processed(event.id)

Design every consumer to be idempotent. Processing the same event twice should have the same effect as processing it once.

## Event Versioning

Your event schemas will evolve. New fields get added. Old fields become obsolete. You need a strategy for handling this without breaking consumers.

Common approaches:

- **Additive changes only** — add new optional fields, never remove or rename
- **Schema registry** — centralized schema management with compatibility checks
- **Version in event type** — `OrderPlacedV1`, `OrderPlacedV2`

Whatever approach you choose, treat event schemas as public APIs. Breaking changes require coordination and migration plans.

## Dead Letter Queues

Some events will fail to process. Maybe the data is malformed. Maybe there’s a bug in the consumer. Maybe a downstream dependency is permanently unavailable.

Don’t let these failures block your queue forever. Route failed events to a dead letter queue where they can be investigated and potentially replayed after fixes.

## Consumer Lag Monitoring

If your consumers fall behind, events pile up. Eventually, you’re processing events from hours or days ago. Users see stale data. Business processes break.

Monitor consumer lag religiously. Alert when it exceeds acceptable thresholds. Have a plan for catching up when consumers fall behind.

## Correlation IDs

When a single user action triggers a cascade of events across multiple services, you need a way to trace them all. Include a correlation ID in every event, propagate it through the entire flow, and use it to stitch together distributed traces.

{  
  "eventType": "OrderPlaced",  
  "correlationId": "abc-123-def-456",  
  "timestamp": "2025-12-28T10:30:00Z",  
  "data": {  
    "orderId": "order-789",  
    "customerId": "cust-012",  
    "items": [...]  
  }  
}

## A Decision Framework

When evaluating whether to use events for a particular interaction, ask these questions:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*V9xqOEaiVm95dUVGRpBv5A.png)

Most systems end up with a hybrid approach: request-response for synchronous user-facing operations, events for background processing and cross-service notifications.

## The Honest Assessment

Event-driven architecture is powerful, but it’s not magic. It trades one set of problems for another:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*QNA0C5hZ6HD2nkGs9115xg.png)

The teams I’ve seen succeed with EDA share common traits:

- They had a genuine problem that events solved (scale, coupling, resilience)
- They invested heavily in observability before writing event code
- They were disciplined about event schema design and versioning
- They used events for the right interactions, not everywhere

The teams that struggled either adopted EDA prematurely, underestimated the operational complexity, or lost sight of the logical flows hidden in their event chains.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*o_3-E7RAB9FBrTWYpG_5Uw.jpeg)

## Key Takeaways

- **“Event-driven” means different things** — be specific about which pattern you’re implementing (notification, state transfer, sourcing, CQRS).
- **Events excel at decoupling and resilience** — when services don’t need to know about each other and eventual consistency is acceptable.
- **Events struggle with immediate consistency** — when users are waiting for an answer, request-response is usually better.
- **The debugging tax is real** — implicit flows across event chains are hard to trace without serious observability investment.
- **Hybrid approaches are normal** — most successful systems use events for some interactions and request-response for others.
- **Operational maturity is prerequisite** — distributed tracing, dead letter queues, and consumer lag monitoring aren’t optional.

_Until next time, keep observing, keep learning._

**— The Architect’s Notebook**

_I’m a Software Engineer learning architecture by watching architects work. If these field notes help you understand architecture better, consider following for more observations every Week._

**What’s your experience with event-driven architecture?** I’d love to hear from other engineers — especially about the debugging challenges or the moment when events finally “clicked” for you. The best insights come from comparing notes.

## References

1. Fowler, M. “What do you mean by ‘Event-Driven’?” [_martinfowler.com_](http://martinfowler.com/), February 2017. [https://martinfowler.com/articles/201701-event-driven.html](https://martinfowler.com/articles/201701-event-driven.html)
2. Fowler, M. “Focusing on Events.” [_martinfowler.com_](http://martinfowler.com/), 2006. [https://martinfowler.com/eaaDev/EventNarrative.html](https://martinfowler.com/eaaDev/EventNarrative.html)
3. Fowler, M. “CQRS.” [_martinfowler.com_](http://martinfowler.com/), July 2011. [https://martinfowler.com/bliki/CQRS.html](https://martinfowler.com/bliki/CQRS.html)
4. Hohpe, G. & Woolf, B. _Enterprise Integration Patterns: Designing, Building, and Deploying Messaging Solutions_. Addison-Wesley, 2003.
5. Kleppmann, M. _Designing Data-Intensive Applications: The Big Ideas Behind Reliable, Scalable, and Maintainable Systems_. O’Reilly Media, 2017.
6. Richards, M. & Ford, N. _Fundamentals of Software Architecture: An Engineering Approach_, 2nd Edition. O’Reilly Media, 2024.
7. Stopford, B. _Designing Event-Driven Systems_. O’Reilly Media, 2018. [https://www.confluent.io/designing-event-driven-systems/](https://www.confluent.io/designing-event-driven-systems/)

[

Event Driven Architecture

](https://medium.com/tag/event-driven-architecture?source=post_page-----2b6faa861334---------------------------------------)

[

Software Development

](https://medium.com/tag/software-development?source=post_page-----2b6faa861334---------------------------------------)

[  
](https://medium.com/tag/software-architecture?source=post_page-----2b6faa861334---------------------------------------)