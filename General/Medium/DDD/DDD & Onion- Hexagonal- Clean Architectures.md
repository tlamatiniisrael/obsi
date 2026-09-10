
# DDD & Onion/ Hexagonal/ Clean Architectures

![](https://miro.medium.com/v2/resize:fit:700/1*0ENyQD1nrCsKNB0ZiIrKYA.jpeg)

## DDD and Architecture Are Orthogonal

**DDD answers:** _What do you put in your code and how do you organize the concepts?_

**Onion/Hexagonal/Clean answer:** _Where do you put the code and how do layers depend on each other?_

One is about **meaning**. The other is about **structure**. You need both.

## The Core Rule All Three Architectures Share

Onion, Hexagonal, and Clean Architecture are essentially the same idea with different diagrams. The one rule they all agree on:

> **Dependencies point inward. Business logic depends on nothing.**

Database → Application → Domain      ✓  correct  
Domain → Database                    ✗  wrong

Your `Order` aggregate should have zero knowledge that MongoDB exists. Your `Resume` aggregate shouldn't import an HTTP library. Business logic is pure — it depends only on itself.

This is exactly what DDD wants too. That’s why they pair so naturally.

## The Layers, Mapped to DDD Concepts

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*2hQvfgj9h8Qnshgw1zEPtQ.png)

DDD tells you _what_ goes in the Domain layer. The architecture tells you _that_ it must stay there, isolated.

## Hexagonal Specifically: Ports and Adapters

Hexagonal has the most useful mental model for understanding the boundary:

**Ports** = interfaces your domain defines. Things like `IResumeRepository`, `IEmailSender`. The domain says "I need something that can do this" — without knowing _how_ it gets done.

**Adapters** = the real implementations. `MongoResumeRepository`, `SendGridEmailSender`. These live in Infrastructure and plug into the ports.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*t41zdeSj6Paka0h9531lAA.png)

In DDD terms: the `Resume` aggregate (see last article) calls `repository.Save(this)` through an interface. Whether that saves to MongoDB, SQLite, or an in-memory dictionary for tests — the aggregate doesn't know and doesn't care.

## Why This Matters Practically

The big practical win: **testability**.

Without this separation, testing your “submit application” logic means spinning up a database, mocking HTTP clients, faking email services. It’s painful so people skip it.

With proper layering + DDD aggregates:

// Testing Resume.Lock() rule — zero infrastructure needed  
resume = Resume(...)  
resume.Lock()  
resume.UpdateSection(...)   // should throw  
  
// That's it. No database. No HTTP. No mocks.  
// The rule is in the aggregate, the aggregate is pure.

You test domain rules as plain logic. Infrastructure gets tested separately, in integration tests.

## The One Sentence Summary

**DDD gives you the _what_ to put in your domain layer. Clean/ Onion/ Hexagonal gives you the _rule_ that the domain layer must stay pure and at the center.**

They’re designed for each other. Most serious DDD practitioners use one of these architectures precisely because DDD’s aggregates and domain logic _need_ a protected layer to live in — otherwise they get contaminated by database concerns and HTTP noise, which defeats the whole point.

> DDD & these architectures evolved _alongside_ and _in response to_ the same problem, with a lot of cross-pollination.

## The Timeline

### **2003 — Eric Evans publishes “Domain-Driven Design”**

This is the book that named and formalized DDD. But Evans wasn’t prescribing a specific architecture. He was describing how to _think_ about business logic.

The layered architecture he suggested in the book was fairly simple — just “don’t let domain logic leak into other layers.” He didn’t solve _how_ to enforce that mechanically.

### **2005 — Alistair Cockburn publishes Hexagonal Architecture**

Cockburn was solving a different frustration independently: “why is my application so hard to test, and why does swapping databases require touching business logic?”

His answer was ports and adapters. He wasn’t specifically responding to Evans — he was responding to the same general industry pain.

### **2008 — Jeffrey Palermo publishes Onion Architecture**

Palermo _was_ explicitly influenced by both DDD and Hexagonal. He wanted a diagram that made the dependency direction more obvious — concentric rings rather than a hexagon. He named the center “Domain Model,” directly borrowing DDD language.

### **2012 — Robert Martin publishes Clean Architecture**

Uncle Bob synthesized Hexagonal, Onion, and several others into Clean Architecture. Again, influenced by the same ideas, slightly different diagram, same core rule.

## What Actually Happened

They all independently noticed the same problem:

> _Business logic kept getting contaminated by infrastructure details, making systems rigid and untestable._

Evans named the _concepts_ that should live at the center. Cockburn, Palermo, and Martin named the _structural rules_ for protecting that center.

They converged because they were solving adjacent facets of the same problem — which is why they fit together so cleanly today.

## The one thing DDD contributed that the others didn’t

The architecture patterns tell you _rules about layers_. DDD told you something deeper: **what should actually be in the center, and how to design it well.**

Without DDD, you could follow Clean Architecture perfectly — strict dependency rules, ports and adapters everywhere — and still end up with an anemic domain layer that’s just a bag of getters and setters with all the real logic sitting in “service” classes. Structurally correct, conceptually hollow.

DDD is what fills the center with actual meaning.

[

Programming

](https://medium.com/tag/programming?source=post_page-----1048d1e0b016---------------------------------------)

[

Coding

](https://medium.com/tag/coding?source=post_page-----1048d1e0b016---------------------------------------)

[

Software Development

](https://medium.com/tag/software-development?source=post_page-----1048d1e0b016---------------------------------------)

[

Software Engineering

](https://medium.com/tag/software-engineering?source=post_page-----1048d1e0b016---------------------------------------)

[

Software Architecture

](https://medium.com/tag/software-architecture?source=post_page-----1048d1e0b016---------------------------------------)

35

[

![#hope](https://miro.medium.com/v2/resize:fill:96:96/1*oaz066AC9TugnDnT4noRPQ.jpeg)



](https://todayamerican.medium.com/?source=post_page---post_author_info--1048d1e0b016---------------------------------------)

[

## Written by #hope

](https://todayamerican.medium.com/?source=post_page---post_author_info--1048d1e0b016---------------------------------------)

[347 followers](https://todayamerican.medium.com/followers?source=post_page---post_author_info--1048d1e0b016---------------------------------------)

·[81 following](https://todayamerican.medium.com/following?source=post_page---post_author_info--1048d1e0b016---------------------------------------)

“If you see something that’s not right, not fair, not just, do something about it. Say something. Do something.” — Rep. John Lewis

Follow