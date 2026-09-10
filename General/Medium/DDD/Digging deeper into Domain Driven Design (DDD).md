
![](https://miro.medium.com/v2/resize:fit:700/1*W2gUWF54xqUQ6sumgf3hVw.jpeg)

## Aggregates as Transaction Boundaries

I told you an aggregate is “a cluster that stays consistent together.” But here’s the part that changes how you _design_:

> **One transaction = one aggregate.**

This sounds like a minor technical rule. But, it’s actually a _design_ rule that forces you to think hard about what truly belongs together.

Say in your app, a user imports a document, and you generate a resume from it. You might instinctively want to do this in one transaction:

1. Save the imported document
2. Create the resume
3. Create all generated sections
4. Update the user’s “last activity” timestamp
5. Log the import event for analytics

But ask yourself: does the user’s “last activity” timestamp _really_ need to be consistent with the resume generation in the same instant? Does the analytics log? No. If the resume saves but the analytics write fails a second later, nobody cares.

**What actually needs to be atomic:** the resume and its sections. That’s your aggregate boundary.

Everything else happens _eventually_ — through domain events. The resume aggregate publishes `ResumeGenerated`, and separate handlers update last-activity and write analytics whenever they get to it.

This is called **eventual consistency**, and it’s not a compromise — it’s recognizing how the business _actually_ works. The business doesn’t say “if we can’t log analytics, undo the resume.” That would be absurd.

**The practical consequence:** your aggregates stay small. Small aggregates = fewer locking conflicts, simpler code, easier testing.

### How to Actually Find Your Aggregates (The Question Technique)

This is where people get stuck. They know the theory but stare at their codebase and think “okay… so what’s my aggregate?”

Ask these questions about each concept:

**“What breaks if these two things are out of sync for even one second?”**

- Resume and its sections? Yes — a resume with orphaned sections is corrupt. Same aggregate.
- Resume and the user’s profile? No — if the profile updates a second after the resume, nothing is broken. Different aggregates.

**“Who’s the gatekeeper?”**

- Can a `Section` exist without a `Resume`? No. Then `Resume` is the root, `Section` is internal.
- Can a `CoverLetter` exist without a `Resume`? Probably yes — they're independent documents. Separate aggregates.

**“If I delete the root, does the child make sense alone?”**

- Delete the resume → sections are meaningless. They’re inside the aggregate.
- Delete the resume → cover letter still has value. Separate aggregate.

## Value Objects Are More Powerful Than You Think

Most people hear “value object” and think “oh, a validated string wrapper.” That undersells it massively.

Value objects can encapsulate _behavior_, not just validation.

``` java
// Not just validation  
money = Money(100, "USD")  
  
// Actual behavior  
total = money.Add(Money(50, "USD"))      // works  
total = money.Add(Money(50, "EUR"))      // throws — can't add different currencies  
discounted = money.ApplyDiscount(0.20)   // returns Money(80, "USD")
```

The currency mismatch rule isn’t in your controller. It’s not in your service layer. It’s in `Money` itself — impossible to bypass.

In your app, think about `SubscriptionTier`. Instead of checking `if (user.tier == "pro") { allow feature }` in thirty places, make it a value object:

``` java
tier = SubscriptionTier("pro")  
tier.CanAccessFeature("document_import")   // true  
tier.MaxResumes()                          // 25  
tier.CanExportAsPDF()                      // true
``` 

Now your business rules about what each tier allows live in _one object_. When you add a new tier or change limits, it’s one file.

## Domain Events: The Secret Weapon for Decoupling

You know events as “something happened.” But let me show you _why_ they transform your architecture.

Without events, your “submit application” code looks like a chain of responsibilities:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*etWxEFGEnJyRoLLsNfbsoA.png)

The problem: your core logic is entangled with five side effects. Want to add a sixth? You edit the submit method. Want to test submission logic? You need to mock an email service, a search index, an analytics tracker…

With events:

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/1*ErXmYsQ1srb0Zv6dXNmFmg.png)

Adding a seventh side effect? Write a new handler. Zero changes to the submission logic. Testing submission? No mocks needed for email or search — they’re not involved.

**This is the real payoff of events.** Your core logic becomes a pure description of “what happens in the business,” free of operational noise.

## The Anti-Corruption Layer (The Coolest Idea in DDD)

This is the concept that makes experienced developers go “oh, **THAT**’S what I’ve been missing.”

Say your app calls an external API — maybe a resume-parsing service or a job board API. That API has _its_ model: weird field names, different structures, maybe `job_listing.compensation.base_annual` nested five levels deep.

If you let that structure leak into your domain, your code now depends on someone else’s design decisions. They change their API; your domain model breaks.

An **anti-corruption layer** is just a translator at the boundary:

// External API returns this mess  
{  
  "job_listing": {  
    "compensation": {  
      "base_annual": 95000,  
      "currency_code": "USD"  
    },  
    "meta": { "posting_id": "abc-123" }  
  }  
}  
  
// Your anti-corruption layer translates it to YOUR language  
JobPosting(  
    id = JobPostingId("abc-123"),  
    salary = Money(95000, "USD")  
)

Your domain never sees the external structure. If the API changes, you fix the translator. Your business logic doesn’t flinch.

**You probably already do this instinctively sometimes** — anytime you’ve written a mapper between an API response and your internal model. DDD just names it and says, “do this deliberately at every boundary.”

## The Mistake That Kills Most DDD Attempts

People learn DDD and immediately try to redesign their entire codebase. It collapses under its own weight.

The winning approach, for someone building a product is:

> **Start with the domain language, not the patterns.**

Before writing any aggregate or value object, sit down and write out — literally in a text file — the words your business uses:

![](https://miro.medium.com/v2/resize:fit:666/1*4M7F8Nu-a7VwLMofGmUH3Q.png)

Then ask: “which of these words mean different things in different parts of my app?” That gives you bounded contexts.

Then ask: “which of these have rules that are getting duplicated?” That gives you your first aggregate or value object candidates.

Then build _one_ slice. Ship it. See how it feels. Expand.

The pattern to internalize: **DDD isn’t about the patterns. It’s about making the invisible rules visible.** Every business has rules that live in people’s heads, in Slack messages, in “oh yeah, you also have to check for that” comments. DDD’s job is to pull those into the code where they can’t be forgotten, bypassed, or duplicated.

[

Programming

](https://medium.com/tag/programming?source=post_page-----1698f814e91c---------------------------------------)

[

Coding

](https://medium.com/tag/coding?source=post_page-----1698f814e91c---------------------------------------)

[

Software Development

](https://medium.com/tag/software-development?source=post_page-----1698f814e91c---------------------------------------)

[

Software Engineering

](https://medium.com/tag/software-engineering?source=post_page-----1698f814e91c---------------------------------------)

[

Software Architecture

](https://medium.com/tag/software-architecture?source=post_page-----1698f814e91c---------------------------------------)

36

1

[

![#hope](https://miro.medium.com/v2/resize:fill:96:96/1*oaz066AC9TugnDnT4noRPQ.jpeg)



](https://todayamerican.medium.com/?source=post_page---post_author_info--1698f814e91c---------------------------------------)

[

## Written by #hope

](https://todayamerican.medium.com/?source=post_page---post_author_info--1698f814e91c---------------------------------------)

[347 followers](https://todayamerican.medium.com/followers?source=post_page---post_author_info--1698f814e91c---------------------------------------)

·[81 following](https://todayamerican.medium.com/following?source=post_page---post_author_info--1698f814e91c---------------------------------------)

“If you see something that’s not right, not fair, not just, do something about it. Say something. Do something.” — Rep. John Lewis

Follow