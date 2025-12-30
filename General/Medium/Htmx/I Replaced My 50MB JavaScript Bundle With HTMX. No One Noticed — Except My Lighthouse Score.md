
I deleted 50MB of JavaScript from production and nothing broke.

**No bugs. No angry Slack thread. No emergency rollback.**

The only thing that changed was the one thing users never stop feeling.  
The page stopped fighting their phone.

> Lighthouse proved it before anyone on the team believed it.

## The Day The Dashboard Started Feeling Heavy

We had an internal dashboard that looked harmless on a big laptop. Tables, filters, search, pagination, a detail panel. The kind of screen people live inside for hours, doing real work, day after day.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*UPUxXHeV_htEqudb)

Typing into the search box sometimes missed letters. Scrolling felt like the browser was dragging a suitcase. Clicking a filter did not fail loudly, it paused just long enough for a user to sigh and try again.

> That kind of slowness is dangerous because it hides.

- The error rate looked normal. Backend latency looked acceptable. Dashboards were green. The complaints were human.
- **The page feels slow.  
    Not broken. Slow.**
- Slow is the defect that makes teams blame everything except themselves. Devices. Networks. Data size. The user. Anything that avoids the obvious answer.

The main thread was busy constantly. Not with user work, but with our work. JavaScript parsing, execution, hydration, state setup, dependency code we did not write running inside a request that should have been a document.

> That was the moment the bundle stopped being a number and started being a weight.

## The Bundle Was Not Features, It Was Feelings

We did what most teams do when a bundle grows.

We justified it.

Charts felt important. A rich table library saved time. A date library avoided bugs. A component kit promised consistency. A client cache made the UI feel fast.

Each choice made sense alone. Together, they became something else.

The bundle filled up with things the page never actually used. Locale data nobody needed. Editor code on a screen that never edited. Utility functions pulled in quietly by dependencies we did not consciously choose.

We ran a bundle analyzer and felt two emotions at once.  
Relief, because the villain finally had a name.  
Embarrassment, because we had normalized it.

The page was not slow because JavaScript is bad.  
It was slow because we shipped a small universe to render a table.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*qSU-TAEWWmI7UW_3)

## What We Expected To Lose

We assumed removing the SPA feel would be obvious.

We thought people would complain about full-page refreshes. We thought navigation would feel old. We thought the UI would lose smoothness. We expected someone to say the word regression.

> That expectation revealed something uncomfortable.

**We were protecting our identity as modern engineers.  
Users were trying to finish work.**

Those are not the same goal.

Once we admitted that, the path became simple. We did not need a new framework. We needed less software between the user and the HTML.

## The Smallest Change That Made Everyone Quiet

We did not start with the entire application. We started with the most complained-about screen.

A user list view. **Search**, **filters**, **pagination**, a list, a detail panel.

We kept the page server-rendered. Real URLs. Real responses. Real HTML. Then we used HTMX only for the parts that had been eating the client.

The interaction model became boring again, in the best possible way.

Type into search and the list updates.  
Change a filter and the list updates.  
Click a page and the list updates.

> No client router. No global state. No hydration. Just a small request that returns a fragment.

```html
<input name="q"  
       hx-get="/users"  
       hx-trigger="keyup changed delay:250ms"  
       hx-target="#list"  
       hx-push-url="true"/>  
<div id="list"></div>
```

That is it.

> The server returns updated HTML for the list. HTMX swaps it into the page. The URL updates so refresh and back buttons behave like adults.

The first time it worked, it felt like cheating.  
The second time, it felt like we had been lying to ourselves about what was necessary.

## The Architecture Became Understandable Again

The new request path looked almost boring.

**Browser requests HTML.**  
**Server renders a fragment.  
HTMX swaps it in place.**

When you show that to another engineer, they understand it instantly. When you show most SPA architectures, you start a lecture.

That contrast mattered.

> Our old setup was impressive. The new setup was clear.  
> Clarity is what survives pressure.

## The Benchmark That Ended The Debate

We measured before and after on the same page, same dataset, same device profile, same throttling. Cold cache and warm cache.

One rule stayed firm. We did not change the backend query. If results improved, it had to be because we removed client weight.

```
Metric (Mobile Profile)                 React Spa (50MB)    Htmx + Ssr  
----------------------------------------------------------------------  
Js Transferred (Gzip)                   6.2 MB             140 KB  
Total Js Executed                       3.1 s              120 ms  
Main-Thread Blocking Time               1,850 ms           190 ms  
Lcp                                     3.9 s              1.8 s  
Inp                                     420 ms             160 ms  
Lighthouse Performance Score            46                 92  
----------------------------------------------------------------------
```

These numbers are not an aesthetic win.  
They are a trust win.

> The browser stopped dropping characters. Scrolling stopped pausing. Filter clicks felt like actions instead of negotiations.

## Why Nobody Noticed, And Why That Was The Point

We shipped the change and waited.

Nothing happened.

> No tickets saying the UI felt different. No complaints about navigation. No questions about why the app now behaved like a normal website.

People just worked.

That silence was the loudest proof.

Users do not notice when you remove your own complexity. They notice when you stop wasting their time.

We expected the absence of JavaScript to be visible.  
The presence of friction had been visible all along.

## The Part We Almost Got Wrong

We made a mistake early.

Our first pass returned too much HTML. It worked, but it was slower than it needed to be. We had moved weight from JavaScript to markup without thinking.

So we tightened it. Only return the fragment that changes. Keep the rest stable. Cache what can be cached. Render only what moves.

That was when the pattern clicked.

HTMX is not magic. It is a sharp tool. Used carelessly, it cuts you.

## The Real Shift Was Responsibility

The deeper change was not performance. It was ownership.

In the SPA model, the browser is the application and the server becomes a JSON vending machine. In the HTMX model, the server is the application and the browser becomes a document that can request pieces of itself.

That shift matters because it moves complexity into a place where we already have discipline. Tracing, logging, caching, query timing, profiling.

We stopped doing performance theatre on the client and started doing performance engineering on the server.

That felt familiar. That felt honest.

## The Sentence That Made Everything Click

Most dashboards do not need a front-end framework.  
They need a faster document.

Say that in a room of engineers and you will get arguments. That is fine. Arguments mean the sentence touched something real.

Our screens of forms and tables were paying a huge tax to behave like apps, even when they did not need to. The cost was paid in battery, latency, jank, and patience. The benefit was paid in engineer comfort.

Once we could measure it, the trade felt wrong.

## The Ending That Actually Matters

This was never a story about HTMX being better than everything else.  
It was a story about honesty.

A dashboard is not a game engine. A list view is not a movie. Most business screens are documents with a few interactions layered on top, and treating them like full applications comes with a quiet cost.

We paid that cost for a long time because everything still worked. It rendered. It shipped. It passed reviews. It just felt heavier every month.

> Deleting 50MB of JavaScript didn’t make the product more impressive.  
> It made it calmer.

Users didn’t praise the change. They didn’t even mention it. They simply stopped fighting the page. That silence mattered more than any compliment.

What surprised me wasn’t the Lighthouse score. It was realizing how often we optimized for ourselves instead of the person on the other side of the screen.

**Most dashboards don’t need a front-end framework.  
They need a faster document.**

If your screen is mostly tables, forms, and filters, it’s worth asking one uncomfortable question:

**Is your JavaScript serving the user, or the team?**

If you’ve tried this and saw different results, share what broke. Those stories are far more interesting than another framework launch.