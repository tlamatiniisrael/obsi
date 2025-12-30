
**Twelve thousand lines of React disappeared in one pull request, and nobody on the team missed them.**

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*IhiyvRYJBOewUfi2)

The old frontend felt heavy.

Every change needed a new hook.

Every bug meant chasing state through three layers of abstractions.

Webpack screamed at us whenever a dependency upgraded.

New hires spent their first two weeks asking why a simple form needed five files and a global store.

By Friday night I was tired enough to say the quiet thought out loud.

What if we try HTMX for a single screen and see how far it goes.

Two days later that single screen had turned into half the dashboard.

By the end of the sprint, React was no longer in the critical path of our product.

HTMX was.

## Why React Started To Feel Like Overkill

React was not a mistake.

It helped when the product was young and everyone wanted to move fast.

We got:

- Components
- Reusable patterns
- A shared mental model

Then the app grew up.

Most of our UI became small, boring pieces of CRUD.

- List records
- Filter them
- Edit a few fields
- Show a toast

The complexity shifted from the browser to the backend.

Permissions, validations, audit logs, workflows.

React stayed at the center even when it stopped being the main problem.

We carried a lot of weight for simple interactions:

- Routing handled by the frontend
- Manual state management for every list and modal
- Custom hooks for things the server already knew how to do

At some point I realised we were running two applications:

One in the browser.

One on the server.

They duplicated each other just enough to create bugs that only appeared in production.

## The Weekend I Gave HTMX A Real Shot

I had seen HTMX mentioned a few times.

The pitch felt almost rude.

Add a few attributes to HTML and your server becomes the state manager again.

No virtual DOM.

No API layer for simple things.

Just HTTP and fragments.

I picked a safe page for an experiment:

- A table of loans
- Filters on top
- A details panel on the right

The React version used:

- React Router
- A data fetching hook
- A context for filters
- A separate component for the details panel

The HTMX version started as a single HTML template.

Here is a simplified core of what replaced a pile of components:

```html
<table id="loan-table">  
  <thead>  
    <tr>  
      <th>Id</th>  
      <th>Customer</th>  
      <th>Status</th>  
    </tr>  
  </thead>  
  <tbody  
    hx-get="/loans"  
    hx-trigger="load, change from:#filters"  
    hx-target="#loan-table tbody"  
    hx-swap="innerHTML">  
  </tbody>  
</table>  
  
<div id="details"  
     hx-get="/loans/first"  
     hx-trigger="load"  
     hx-target="#details"  
     hx-swap="innerHTML">  
</div>
```

And a simple Spring controller handled the server side:

```java
@GetMapping("/loans")  
public String loans(Model model,  
                    @RequestParam Map<String, String> filters) {  
    List<Loan> loans = loanService.find(filters);  
    model.addAttribute("loans", loans);  
    return "fragments/loan-table-body";  
}  
  
@GetMapping("/loans/{id}")  
public String loanDetails(@PathVariable Long id, Model model) {  
    Loan loan = loanService.get(id);  
    model.addAttribute("loan", loan);  
    return "fragments/loan-details";  
}
```

No JSON.

No client side state.

Just HTML fragments rendered on the server and swapped into the page.

By Sunday evening the page behaved exactly like the React version.

Bundle size went from hundreds of kilobytes of JavaScript to a small script tag with HTMX and a few lines of local glue.

Time to first meaningful paint dropped in a way that users actually felt.

More important than numbers, ==the code finally matched how the team already thought about the system.==

## How The Architecture Shifted

React had forced us into a certain mental shape.

With HTMX, the sketch on the whiteboard suddenly matched the runtime reality.

Here is roughly how it looks now:

```
+-----------------------+  
|   Browser + HTMX      |  
|  - Simple HTML        |  
|  - hx-* attributes    |  
+-----------+-----------+  
            |  
            | HTTP GET / POST (HTML fragments)  
            v  
+-----------------------+  
|   Spring Controllers  |  
|  - Read inputs        |  
|  - Call services      |  
|  - Return templates   |  
+-----------+-----------+  
            |  
            v  
+-----------------------+  
|   Services + DB       |  
|  - Business rules     |  
|  - Transactions       |  
+-----------------------+
```

No separate API for most screens.

No extra mapping between client state and server models.

If something looked wrong in the UI, we opened the same template that the server rendered.

That gave three big wins:

1. **Onboarding became faster**  
    New developers no longer had to learn a custom React setup before shipping value.  
    If they knew HTML and had touched a server side framework, they could contribute.
2. **Bugs became easier to reason about**  
    When a list did not update, we checked the server route and the template.  
    We did not have to walk through a hook chain to see where state diverged.
3. **Features shipped in smaller steps**  
    We could add one small `hx-post` or `hx-get` to existing markup instead of planning a larger refactor of component trees.

## Some Numbers That Won The Argument

Engineers trust stories.

Leadership trusts numbers.

For our main internal dashboard page, this is what we measured after the migration of that screen:

- Initial HTML load: from around 2.4 seconds to under 900 milliseconds
- JavaScript shipped: from roughly 350 KB to under 40 KB
- Time for a simple filter change: from 400 to 80 milliseconds p95

These numbers were not from a marketing slide.

They came from the same Grafana graphs that had been quietly judging our React setup for months.

After that, nobody asked why we were replacing React with HTMX.

They asked which screen we were rewriting next.

## Where HTMX Shines And Where React Still Wins

HTMX is not a silver bullet.

It shines when:

- Your app is mostly forms, lists, and buttons
- The server already knows all the important rules
- You want to keep the mental model simple

React still makes sense when:

- You build something closer to a desktop app in the browser
- You need complex local interactions that cannot round trip to the server frequently
- You share components across many teams and products

At our stage, most of our pain came from forcing simple workflows into a heavy single page mindset.

HTMX brought us back to a web that felt honest.

Request in.

HTML out.

## If You Want To Try This Next Weekend

Pick one screen that hurts.

A simple table.

A form with a few steps.

Anything that currently needs too much JavaScript for what it delivers.

Rewrite only that screen with HTMX and server rendered fragments.

Measure:

- Bundle size
- Time to first meaningful paint
- Time to first interaction

If the numbers shift and the code feels lighter in your hands, you will know why a weekend with HTMX was enough for us to quietly replace an entire React frontend.

[

React

](https://medium.com/tag/react?source=post_page-----406d10b7cb4c---------------------------------------)

[

Web Development

](https://medium.com/tag/web-development?source=post_page-----406d10b7cb4c---------------------------------------)

[

Programming

](https://medium.com/tag/programming?source=post_page-----406d10b7cb4c---------------------------------------)

[

Technology

](https://medium.com/tag/technology?source=post_page-----406d10b7cb4c---------------------------------------)

[

Htmx

](https://medium.com/tag/htmx?source=post_page-----406d10b7cb4c---------------------------------------)

528

12

[

![The Concurrent Mind](https://miro.medium.com/v2/resize:fill:96:96/1*hk3ovL5bc27Ok4Gt4ifmEw.jpeg)



](https://medium.com/@kp9810113?source=post_page---post_author_info--406d10b7cb4c---------------------------------------)

[

## Written by The Concurrent Mind

](https://medium.com/@kp9810113?source=post_page---post_author_info--406d10b7cb4c---------------------------------------)

[2.6K followers](https://medium.com/@kp9810113/followers?source=post_page---post_author_info--406d10b7cb4c---------------------------------------)

·[6 following](https://medium.com/@kp9810113/following?source=post_page---post_author_info--406d10b7cb4c---------------------------------------)

Full-Stack Developer crafting clean, modern, and high-performance code

Follow