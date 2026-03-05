
_The Observer’s field notes on making documentation that doesn’t collect dust_

I was sitting in an architecture review last month when our lead architect pulled up a Confluence page that hadn’t been updated in two years. “This is our system architecture,” she said, then immediately added, “but don’t trust any of it.”

Everyone laughed. Nobody was surprised.

Here’s the uncomfortable truth: most architecture documentation is written once and forgotten. It becomes a historical artifact the moment it’s published — a snapshot of what someone _thought_ the system looked like at some point in the past.

But I’ve also seen teams where documentation actually works. Where new engineers get up to speed in days instead of weeks. Where architecture decisions don’t get relitigated every quarter. Where diagrams match reality.

The difference isn’t effort or good intentions. It’s approach.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*-Kn42mus5cLnvmDMqm_Lqg.jpeg)

## Why Most Architecture Documentation Fails

According to IcePanel’s 2024 State of Software Architecture Report (based on 96 respondents from the architecture community), the vast majority use diagramming tools and collaborative wikis to document their architecture. Yet some respondents admitted they have _no single source of truth_ for their architecture documentation.

Let that sink in. Almost everyone documents. But not everyone trusts what they’ve documented.

The problem isn’t that teams don’t try. It’s that traditional documentation approaches are fundamentally broken:

**The “Big Bang” approach:** Someone spends weeks creating comprehensive documentation. It’s beautiful. It’s thorough. It’s outdated by the time it’s reviewed.

**The “Wiki Graveyard” approach:** Documentation lives in Confluence or Notion, scattered across dozens of pages with no clear structure. Finding anything requires tribal knowledge about where to look.

**The “Diagram Dump” approach:** Architecture exists as a collection of Visio or [draw.io](http://draw.io/) files with no context, no decisions explained, and no indication of what’s current versus aspirational.

I’ve watched architects pour hours into documentation that nobody reads. The frustration is real. But the solution isn’t to document _more_ — it’s to document _differently_.

## The C4 Model: Zoom Levels for Architecture

One of the most practical frameworks I’ve seen architects use is the C4 model, created by Simon Brown. The core insight is simple: different audiences need different levels of detail.

The C4 model provides four zoom levels:

**Level 1: System Context** — The 30,000-foot view. Shows your system as a box, surrounded by the users and external systems it interacts with. This is what you show to executives and new team members on day one.

**Level 2: Container** — Zooms into your system to show the major technical building blocks: applications, databases, message queues, file systems. This is the level most useful for day-to-day architecture discussions.

**Level 3: Component** — Zooms into a single container to show its internal components. Useful for detailed design discussions within a team.

**Level 4: Code** — The actual code structure. Usually auto-generated from the codebase rather than manually maintained.

According to IcePanel’s survey, Container diagrams (Level 2) are the most commonly used, followed by Context diagrams (Level 1). This makes sense — Level 2 hits the sweet spot of useful detail without overwhelming complexity.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*hmzzbKfWfkJ2TXTJR0oUNw.png)

The power of C4 isn’t the diagrams themselves — it’s the shared vocabulary. When someone says “container,” everyone knows they mean a deployable unit, not a Docker container. When someone asks for a “context diagram,” everyone knows the level of abstraction expected.

## Architecture Decision Records: Capturing the “Why”

Diagrams show _what_ the architecture looks like. But they don’t explain _why_ it looks that way.

This is where Architecture Decision Records (ADRs) come in. Originally proposed by Michael Nygard in 2011, ADRs are short documents that capture significant architectural decisions along with their context and consequences.

The IcePanel survey found that about half of respondents use ADRs — which means roughly half the industry has discovered this practice, while the other half is still wondering why the same decisions keep getting relitigated.

A basic ADR follows this structure:

```
# ADR-001: Use PostgreSQL for Primary Data Store  
  
## Status  
Accepted  
  
## Context  
We need a primary database for our order management system.   
The system will handle approximately 10,000 orders per day   
with complex queries for reporting. Our team has strong   
SQL experience but limited NoSQL expertise.  
  
## Decision  
We will use PostgreSQL as our primary data store.  
  
## Consequences  
**Positive:**  
- Strong ACID compliance for order integrity  
- Excellent query performance for complex reports  
- Team familiarity reduces ramp-up time  
  
**Negative:**  
- Horizontal scaling is more complex than NoSQL alternatives  
- May need to revisit if order volume grows 10x  
  
**Neutral:**  
- Will need to set up replication for high availability
```

The magic of ADRs is in the _Context_ section. Six months from now, when someone asks “why didn’t we use NoSQL ?”, the answer is right there. No archaeology required.

I’ve watched teams adopt ADRs and the effect is almost immediate: fewer circular debates, faster onboarding, and decisions that actually stick.

## Arc42: A Complete Documentation Template

If C4 gives you a vocabulary for diagrams and ADRs capture decisions, arc42 provides a complete template for everything else.

Created by Gernot Starke and Peter Hruschka, arc42 is a pragmatic template with 12 sections covering everything from introduction and goals to risks and technical debt. It’s been widely adopted across the industry and is available for free.

The arc42 sections:

1. **Introduction and Goals** — Requirements, quality goals, stakeholders
2. **Constraints** — Technical, organizational, and political constraints
3. **Context and Scope** — System boundaries and external interfaces
4. **Solution Strategy** — Fundamental decisions and solution approaches
5. **Building Block View** — Static decomposition (this is where C4 fits)
6. **Runtime View** — Behavior and scenarios
7. **Deployment View** — Infrastructure and deployment
8. **Crosscutting Concepts** — Patterns and approaches used across the system
9. **Architectural Decisions** — Important decisions (ADRs live here)
10. **Quality Requirements** — Quality scenarios and metrics
11. **Risks and Technical Debt** — Known issues and concerns
12. **Glossary** — Domain and technical terms

You don’t need to fill out every section for every project. Arc42 is explicitly designed to be tailored — use what’s relevant, skip what isn’t. But having the template means you won’t forget important aspects.

## Docs-as-Code: Making Documentation Maintainable

Here’s an observation from watching architects struggle with documentation: the tools matter more than you’d think.

When documentation lives in a wiki, it’s disconnected from the code it describes. Updates require context-switching. Reviews happen informally (or not at all). There’s no history of who changed what and why.

The “docs-as-code” approach treats documentation like source code:

- **Plain text formats** (Markdown, AsciiDoc) instead of proprietary formats
- **Version control** (Git) for history and collaboration
- **Code review** for documentation changes
- **Automated publishing** from CI/CD pipelines
- **Living alongside the code** it describes

```
project/  
├── src/  
│   └── ...  
├── docs/  
│   ├── architecture/  
│   │   ├── context.md  
│   │   ├── containers.md  
│   │   └── decisions/  
│   │       ├── 001-database-choice.md  
│   │       ├── 002-api-versioning.md  
│   │       └── 003-authentication.md  
│   └── diagrams/  
│       └── system-context.puml  
└── README.md
```

When documentation lives in the same repository as code, something interesting happens: developers actually update it. Not because they suddenly love writing docs, but because it’s _right there_. The friction disappears.

Tools like Structurizr (for C4 diagrams as code), PlantUML, and Mermaid let you define diagrams in text files that can be version-controlled and automatically rendered.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*vOUbyxmXHKGF5GrSRMSzkA.jpeg)

## The Minimum Viable Documentation

I’ve seen teams paralyze themselves trying to document everything. Don’t.

Start with the minimum that provides value:

**For a new project:**

1. One context diagram (C4 Level 1)
2. One container diagram (C4 Level 2)
3. ADRs for the first 3–5 significant decisions
4. A README that explains how to run the system

**For an existing project:**

1. Start with a context diagram — even if imperfect, it sparks conversations
2. Document the _next_ significant decision as an ADR
3. Add container diagrams for the parts causing the most confusion

The goal isn’t comprehensive documentation. It’s _useful_ documentation that evolves with the system.

## Keeping Documentation Alive

The IcePanel survey revealed that teams update their architecture documentation at wildly different cadences — some weekly, some monthly, some quarterly. There’s no single right answer, but there are patterns that work:

**Trigger-based updates:** Update documentation when something changes, not on a schedule. New service? Update the container diagram. Major decision? Write an ADR.

**Review gates:** Make documentation review part of your architecture review process. The survey found many teams do formal architecture reviews only once a year or less — that’s probably not enough.

**Ownership:** Assign clear ownership for documentation sections. Unowned documentation is unmaintained documentation.

**Lightweight validation:** Periodically ask: “Does this diagram match reality?” If the answer is “mostly,” that’s often good enough. Perfect accuracy isn’t the goal — useful accuracy is.

## Key Takeaways

- **Use C4 for diagrams** — The four levels (Context, Container, Component, Code) give you a shared vocabulary and appropriate detail for different audiences
- **Capture decisions in ADRs** — Short documents explaining the context, decision, and consequences prevent endless relitigating
- **Consider arc42 as a template** — The 12-section structure ensures you don’t forget important aspects, but use only what’s relevant
- **Treat docs as code** — Version control, code review, and living alongside the codebase dramatically improves maintainability
- **Start minimal, evolve incrementally** — A context diagram and a few ADRs beats a comprehensive document nobody reads

## References

1. Brown, S. “The C4 model for visualising software architecture.” [_c4model.com_](http://c4model.com/), 2024. [https://c4model.com/](https://c4model.com/)
2. IcePanel. “State of Software Architecture Report 2024.” _IcePanel_, November 2024. [https://icepanel.io/blog/2024-11-26-state-of-software-architecture-2024](https://icepanel.io/blog/2024-11-26-state-of-software-architecture-2024)
3. Starke, G. & Hruschka, P. “arc42 Template Overview.” [_arc42.org_](http://arc42.org/), 2024. [https://arc42.org/overview](https://arc42.org/overview)
4. Nygard, M. “Documenting Architecture Decisions.” _Cognitect Blog_, November 2011. [https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
5. PlatformOS. “The State of Developer Experience in 2024: The Role of Documentation.” _PlatformOS_, 2024. [https://www.platformos.com/blog/post/the-state-of-developer-experience-in-2024-the-role-of-documentation](https://www.platformos.com/blog/post/the-state-of-developer-experience-in-2024-the-role-of-documentation)

_Until next time, keep observing, keep learning._

**— The Architect’s Notebook**

_I’m a Software Engineer learning architecture by watching architects work. If these field notes help you understand architecture better, consider following for more observations every Week._

**What’s your experience with architecture documentation?** I’d love to hear what’s worked (or spectacularly failed) for your team. Do you use ADRs? Has docs-as-code changed how your team maintains documentation? The best insights come from comparing notes.