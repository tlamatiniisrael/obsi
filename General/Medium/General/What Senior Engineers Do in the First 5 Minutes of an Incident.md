
## _It’s not what you think._

![](https://miro.medium.com/v2/resize:fit:700/1*KU4GS8U-0HpUgIX7Mg0K0g.png)


**What Senior Engineers Do in the First 5 Minutes of an Incident**

_It’s not what you think._

The first time I got paged at 3AM, I did everything wrong.

I opened every dashboard at once.

I restarted the service.

I Googled the error message.

I told the team “looking into it” — which was a lie, because I had no idea what I was looking at.

45 minutes later, I found the issue.

A misconfigured connection pool. 3 lines of config. Fixed in 30 seconds.

45 minutes of panic for 30 seconds of work.

The second time I got paged at 3AM, I watched a senior engineer handle it.

Same kind of incident. Different outcome.

He was resolved in 11 minutes.

I watched him the entire time. He didn’t open more dashboards than me. He didn’t know more than me.

He just moved differently.

**What he did in the first 5 minutes**

He didn’t touch anything.

For the first 90 seconds, he just read.

Error messages. Timestamps. Which service. Which endpoint. When it started.

He said one thing out loud:

“Okay. It started at 2:47. What changed at 2:47?”

That was it. That was the whole framework.

Not “what’s broken.”

“What changed.”

He pulled the deployment log.

2:44 AM — deployment to production.

3 minutes before the incident started.

He didn’t need to look at anything else.

“It’s the deploy. Roll it back.”

Rollback at 3:02 AM. Service recovered at 3:04 AM.

11 minutes. Start to finish.

**The difference**

I was asking “what’s broken?”

He was asking “what changed?”

Those are completely different questions.

“What’s broken” sends you into every dashboard, every log, every metric. Infinite surface area. No direction.

“What changed” gives you a timeline. A starting point. A hypothesis to test.

Production systems don’t randomly break.

Something changes. Then something breaks.

Find the change. You find the cause.

**What senior engineers actually do in the first 5 minutes**

They don’t fix anything.

They establish a timeline.

When did it start? What does “broken” actually mean — errors, latency, complete outage? Who is affected — all users, specific regions, specific features? What changed in the last 30 minutes — deployments, config changes, traffic spikes, scheduled jobs?

That’s it. Four questions. Five minutes.

Everything after that is execution, not investigation.

Junior engineers skip this step because it feels slow.

Senior engineers never skip it because they’ve learned what happens when you don’t.

You restart things that didn’t need restarting.

You chase metrics that aren’t the problem.

You make things worse trying to make things better.

**The framework I use now**

First 60 seconds: read, don’t touch. Understand the shape of the problem before you do anything.

Next 2 minutes: build a timeline. When did it start? What changed before it started?

Next 2 minutes: form one hypothesis. Not five. One. The most likely cause based on what changed.

Minute 5: test the hypothesis or escalate if you can’t.

That’s the whole system.

It doesn’t require knowing more.

It requires moving in the right order.

**Why most engineers never learn this**

Because nobody teaches it.

Bootcamps teach you to build features.

Tutorials teach you happy path.

Your senior engineer is too busy fixing the incident to explain their thought process while they’re doing it.

So you learn by watching. Slowly. Over years.

Or you don’t learn it at all and you keep spending 45 minutes on 30-second fixes.

I spent 3 years collecting these patterns.

Every incident I handled. Every senior engineer I watched. Every postmortem I read.

I turned it into a system.

It’s called the **Production Engineer System** — everything senior engineers know about handling incidents, structured so you can use it under pressure.

Not theory. Not generic advice.

The exact sequence. The exact questions. The exact frameworks.

For the moment when production is down and your brain isn’t fully working.

→ [Production Engineer System](https://devrimozcay.gumroad.com/l/syludu)

And if you want the pattern recognition that comes from seeing 80+ real incidents — what actually causes them, how they were fixed, what should have prevented them:

→ [Production Engineer OS](https://devrimozcay.gumroad.com/l/xzkwbf)

_I write about production failures, incident response, and what senior engineers actually know. Follow for more._

[Programming](https://medium.com/tag/programming?source=post_page-----d80a56585651---------------------------------------)

[Java](https://medium.com/tag/java?source=post_page-----d80a56585651---------------------------------------)

[Software Engineering](https://medium.com/tag/software-engineering?source=post_page-----d80a56585651---------------------------------------)

[Software Architecture](https://medium.com/tag/software-architecture?source=post_page-----d80a56585651---------------------------------------)

[Coding](https://medium.com/tag/coding?source=post_page-----d80a56585651---------------------------------------)

12

2

[![Engineering Playbook](https://miro.medium.com/v2/resize:fill:48:48/1*zACrMGYAWryjYXgJs_52mw.png)](https://medium.com/engineering-playbook?source=post_page---post_publication_info--d80a56585651---------------------------------------)

[## Published in Engineering Playbook](https://medium.com/engineering-playbook?source=post_page---post_publication_info--d80a56585651---------------------------------------)

[454 followers](https://medium.com/engineering-playbook/followers?source=post_page---post_publication_info--d80a56585651---------------------------------------)

·[Last published May 3, 2026](https://medium.com/engineering-playbook/python-datetime-part-2-formatting-parsing-and-unix-timestamps-3521a3f5a091?source=post_page---post_publication_info--d80a56585651---------------------------------------)

We write about software engineering, backend architecture, DevOps, cloud systems, AI engineering, system design, performance tuning, debugging, production failures, and the lessons learned the hard way. If you’re building something real — this is your playbook.

Follow

[![Tech Brand](https://miro.medium.com/v2/resize:fill:48:48/1*omLH_AwJuQiOMwh1uasL4Q.png)](https://medium.com/@developer_programmer?source=post_page---post_author_info--d80a56585651---------------------------------------)

[## Written by Tech Brand](https://medium.com/@developer_programmer?source=post_page---post_author_info--d80a56585651---------------------------------------)

[751 followers](https://medium.com/@developer_programmer/followers?source=post_page---post_author_info--d80a56585651---------------------------------------)

·[190 following](https://medium.com/@developer_programmer/following?source=post_page---post_author_info--d80a56585651---------------------------------------)

Software Engineer writing about Java, Spring Boot, backend systems, performance, architecture, and real-world production challenges.

Follow