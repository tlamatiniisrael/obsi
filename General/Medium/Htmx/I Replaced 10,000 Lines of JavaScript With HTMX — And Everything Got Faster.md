
# I Replaced 10,000 Lines of JavaScript With HTMX — And Everything Got Faster

## Why trading JSON for HTML was the best architectural decision of 2025.

**The hardest part wasn’t deleting the JavaScript.**

The hardest part was admitting that thousands of lines of code were solving a problem HTMX made irrelevant.

HTML over the wire — an old idea with a new name — removed the layers we defended for years.  
It simplified rendering, reduced latency, and made our UI feel immediate again.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*qC0JEikvDVrKShx4)

## Why 10,000 Lines Existed in the First Place

We built our UI on a familiar pattern: client-side state, JSON fetches, and a pile of handlers to keep screens in sync.  
The complexity didn’t come from features. It came from managing everything the browser had to “know.”

**Client state meant:**

- stores
- reducers
- lifecycle handlers
- repeated fetch calls
- duplicated validation logic

As the product grew, the frontend became slower to ship, slower to review, and strangely fragile.

> HTMX’s model was the opposite:  
> **the server owns the truth, and the browser swaps HTML fragments.**  
> No controllers. No stores. No diff engine.

## What HTML Over the Wire Actually Means

HTML over the wire:  
**The server returns ready-to-render HTML instead of JSON.**  
The browser replaces a target element using attributes like `hx-get`, `hx-post`, or `hx-swap`.

Why it matters:

- Removes most client logic
- Uses server templates (already tested)
- Reduces hydration work
- Avoids re-implementing domain logic on the client
- Works incrementally

This section shows the exact places where the simplification paid off.

## Rebuilding an Overengineered Search Box

The original search box required:

- a debounced fetch call
- a JSON API
- a “results” reducer
- template rendering on the client
- keyboard navigation handlers

With HTMX, we reduced it to one server endpoint + a single attribute.

## Before (JSON + JS)

```js
searchInput.addEventListener("input", debounce(async e => {  
  const q = e.target.value;  
  const res = await fetch(`/api/search?q=${q}`);  
  const data = await res.json();  
  results.innerHTML = renderResults(data.items);  
}, 200));
```

## After (HTMX)

```js
<input hx-get="/search"  
       hx-trigger="keyup changed delay:200ms"  
       hx-target="#results">  
<div id="results"></div>
```

## What changed

- The server returned an HTML fragment.
- The browser replaced the `#results` div.
- No reducer. No render function. No state logic.

## Impact

Rendering time for results dropped from **72ms** to **11ms**.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*Yo4948AtmuSpgT1M)

## Diagram: How the Flow Simplified

```
Before (JSON)  
   -------------  
   [Browser JS] --fetch--> [API]  
        |                    |  
   parse JSON          build JSON  
        |                    |  
   render client        template server  
        |                    |  
       DOM <------diff-------|
```

```
After (HTMX)  
   ------------  
   [Browser] --hx-get--> [Server templates]  
        |                     |  
        |               render HTML  
        |                     |  
       <-----swap fragment----|
```

> Less parsing. No diff engine. No duplicated logic.

## Killing the Client Router

Our previous SPA router handled:

- history
- active tab highlighting
- scroll state
- conditional fetches
- loading placeholders

Every new route touched 4–6 files.

> HTMX replaced it using `hx-push-url` and `hx-target`.

## New Page Load Example

```js
<a hx-get="/settings"  
   hx-push-url="true"  
   hx-target="#view">  
   Settings  
</a>  
<div id="view"></div>
```

## Why this mattered

State transitions lived in one place: the server route.  
The browser became a thin layer that knew how to swap content.

## Deep effect

Our “router” file went from **1387 lines** to **42 lines** (only a helper for scroll restoration remained).

## Cutting the API Layer in Half

We didn’t remove the API entirely — mobile clients still needed it.  
But we removed every endpoint built “just for the frontend to re-render a list/card/table.”  
For example, this API:

```js
func ListUsers(w http.ResponseWriter, r *http.Request) {  
    users := db.AllUsers()  
    json.NewEncoder(w).Encode(users)  
}
```

Became this server-rendered handler:

```js
func ListUsersHTML(w http.ResponseWriter, r *http.Request) {  
    users := db.AllUsers()  
    render("users/list.html", users, w)  
}
```

## Practical benefits

- API count dropped from 94 → 51.
- Tests reduced by 40%.
- Versioning pain disappeared.

## Nuance

When clients genuinely need structured data, keep JSON.  
HTMX should replace unnecessary front-facing endpoints, not core APIs.

## The Table That Convinced the Team

We benchmarked the most active dashboard screen: a table with filters and pagination.

```
+---------------------------+---------+---------+  
| Metric                    | JSON/JS |  HTMX   |  
+---------------------------+---------+---------+  
| Avg. render time          |  131ms  |  46ms   |  
| Lines of frontend code    |   420   |   52    |  
| API calls per interaction |    3    |    1    |  
| Bug reports per quarter   |   14    |    3    |  
+---------------------------+---------+---------+
```

The column that mattered most wasn’t speed.  
It was stability.  
Every new feature stopped breaking something two screens away.

## Why HTMX Felt Faster Than React, Vue, and Vanilla JS

Not because HTMX is “better.”  
But because HTMX does far less.

Here’s what the browser no longer did:

- no diffing
- no component lifecycle
- no hydration
- no virtual DOM bookkeeping
- no client template construction

## Server-rendered HTML is predictable

Templates had already been reviewed.  
They already passed tests.  
The browser simply placed them.

## Edge case

If your UI requires highly interactive, nested components (drag-drop boards, IDE-like apps), HTMX alone is not enough.  
HTMX wins when the UI is structured around forms, lists, tables, icons, and links — not complex canvas or rich editors.

## The “Progressive Enhancement” Everyone Pretends to Use

Our old frontend claimed to support progressive enhancement.  
It didn’t.  
Without JS, almost nothing worked.

HTMX changed that without effort.

## Example

```html
<form hx-post="/update" hx-target="#row-{{id}}">  
    <input name="email" value="{{email}}">  
    <button>Save</button>  
</form>
```

If HTMX is missing:

- The form submits normally.
- The server updates.
- The user gets redirected.

This fallback is honest.  
It reduced our error states (especially offline or flaky connections) by 27%.

## The Surprising Part: Developers Started Shipping Faster

The first month after migration, PR size dropped sharply.

```
+------------------------+---------+---------+  
| PR Metric              | Before  | After   |  
+------------------------+---------+---------+  
| Avg. PR lines changed  |  510    |   91    |  
| Avg. review time       |  3.1h   |  41min  |  
| Rollback frequency     |  11/mo  |   3/mo  |  
+------------------------+---------+---------+
```

## Why it happened

- No component boilerplate
- No prop drilling
- No memoization debates
- No client-side error branching
- No parallel backend + frontend changes

Feature teams wrote one clear template.  
Backend owners controlled rendering.  
The number of “half-broken UI states” steadily dropped.

## A Larger Architectural Lesson

HTMX didn’t speed up our product because it’s new.  
It helped because we stopped forcing frontend code to replicate backend logic.

**When two layers try to represent the same state, the UI loses.**

The next diagram shows how our model simplified:

```
Before  
------  
[Backend rules]  
    |  
    v  
[JSON API] ---> [Client state manager]  
                     |  
                     v  
                [UI renderer]  
  
After  
-----  
[Backend rules]  
     |  
     v  
[Server templates] ---> [Browser swaps]
```

Removing one state layer removed entire categories of bugs.

## When You Should NOT Use HTMX

Use JSON + JS when:

- You need offline-first UX
- You depend on local caching
- You do heavy client rendering
- You run large canvas/WebGL workloads
- You manage long-lived client state

HTMX is not the answer for:

- dashboards with real-time charts
- multiplayer UIs
- collaborative editors

Use HTMX when:

- forms rule your UI
- you mostly fetch + display data
- templates live on your server
- scroll + pagination + filters dominate interactions

## The One Migration Strategy That Worked

We didn’t rewrite the app.  
We replaced endpoints one by one.

## How

1. Choose a page with brittle JSON → JS logic.
2. Add an HTML-rendering endpoint next to the existing API.
3. Replace the fetch call with `hx-get`.
4. Observe errors in network tab.
5. Delete the JS only when stable.

## Why this matters

HTMX works incrementally.  
You don’t need to burn your SPA or rip out bundlers.

The real win is the gradual removal of complexity.

## Final Takeaway

Deleting 10,000 lines of JavaScript wasn’t the achievement.  
The real achievement was letting the server render again.  
HTMX didn’t give us magic.  
It gave us less work.  
Less state duplication.  
Fewer moving pieces.  
And faster screens.

The outcome surprised us:  
**simpler code became faster code.**

If your frontend feels heavier than the problem demands, try replacing one JSON endpoint with one HTML fragment.  
See how it feels.  
You might delete more than you expect.