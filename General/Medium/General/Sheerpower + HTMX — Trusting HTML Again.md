
_Why a tiny server and a tiny library can outdeliver heavyweight frameworks in clarity, speed, and maintainability._

There’s a strange peace in building something simple on the web today.

Not boring — _simple._

The kind of simple where you understand every part of the system.  
Where clicking a button doesn’t trigger seven layers of invisible state transitions.  
Where **“inspect element” actually helps you debug.**

That’s the world you enter when **Sheerpower** and **HTMX** work together.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*nLQC3nLDbcAOfTZoSBlPYQ.png)

Most developers have become so used to shipping large JavaScript bundles and maintaining complex APIs that they forget the web’s original strength:

> **_Servers send HTML. Browsers render HTML._**

This pairing — _Sheerpower on the server, HTMX in the browser_ — brings that philosophy back with a modern twist.

No performance compromises.  
No loss of interactivity.  
No developer pain.

This is not nostalgia for the “old web.”  
This is what the modern web _could_ be when you trust it again.

## The Core Idea

**Sheerpower**: keeps a FastCGI handler alive, loads data once, and responds instantly.  
**HTMX**: sends micro-requests and swaps HTML into the page.

No JSON.  
No hydration.  
No client-side state machines.

Just **HTML in → HTML out**.

## What Sheerpower Actually Is

Before going deeper, let’s clear up what Sheerpower really offers.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/1*oTF4gMBSiPl7frjTM6_gVw.png)

### Sheerpower is a programming language built for business applications.

It focuses on:

- **fast compilation**
- **precise math** (16 digits guaranteed)
- **massive data throughput**
- **clear, readable syntax**

You can build dictionary lookups, search engines, analytics utilities, and data-heavy APIs that respond in sub-millisecond time — without complexity.

### Note for Beginners

In Sheerpower:

- your code expresses _intent_
- the language handles memory + concurrency
- you don’t worry about low-level systems
- everything is optimized behind the scenes

This lets you build **clean business code** without needing to engineer the runtime.

## The Sheerpower Runtime Model

      ┌────────────────────────────────┐  
      │     Sheerpower FastCGI App     │  
      ├────────────────────────────────┤  
      │  Starts ONCE                   │  
      │  Loads TSV/Config/Data ONCE    │  
      │  Builds Clusters (in-memory)   │  
      ├────────────────────────────────┤  
      │  Receives Request → Responds   │  
      │  (no reboot, no reload)        │  
      └────────────────────────────────┘

This alone eliminates **90%** of the overhead found in typical web servers.

## Why This Matters in 2025

Modern web stacks are powerful — but **bloated**.

We have:

- bundlers
- hydration frameworks
- diffing algorithms
- client-side routers
- 80 MB node_modules folders
- build pipelines
- endless plugins

And every tool promises to fix the problems it _just added._

Meanwhile, for **80% of apps** (dashboards, CRUD tools, admin panels, search UIs), plain HTML is enough.

HTMX + Sheerpower bring sanity back:

- the server renders HTML
- the browser swaps it
- no duplication of logic
- no API glue code
- no frontend bundle

This isn’t anti-JS.  
It’s **pro-simplicity**.

## Sheerpower: A Server That Respects Time

### 1. Persistent FastCGI Handlers

Boot once.  
Load once.  
Serve forever.

No re-reading files.  
No reconstructing structures.  
No repeated parsing.

### 2. Clusters (In-memory Structures)

Load a TSV or dataset:

```
cluster words: word$, def$  
cluster input name "@words.tsv", headers 0: words
```

Boom — done.  
Sheerpower builds a high-performance in-memory index.

Perfect for:

- dictionaries
- search indexes
- configuration maps
- knowledge bases
- preloaded datasets

### 3. Straightforward Templates

HTML with simple `[[variable]]` markers.

No “templating language.”  
No DSL.  
No JSX.

### 4. Zero Mental Overhead

You can leave a Sheerpower project for 6 months, return, and understand everything.

That’s rare today.

## Diagram: HTMX + Sheerpower Request Flow

```
┌───────────────┐       hx-get/hx-post       ┌────────────────────┐  
│   Browser     │ ─────────────────────────▶ │  Sheerpower Server │  
│ (HTML + HTMX) │                            │ (Data in Memory)   │  
└──────┬────────┘                            └───────────┬────────┘  
       │                                             HTML fragment  
       │                   ◀─────────────────────────────  
       ▼  
┌──────────────────────────────────────────┐  
│ HTMX swaps returned HTML into target div │  
└──────────────────────────────────────────┘
```

No JSON.  
No hydration.  
No client rebuild.

## The Sheerpower Way

### 1. Loading a TSV File Into Memory (Correct Syntax)

Sheerpower loads and parses a TSV file into an in-memory cluster with just two statements:

```
cluster words: word$, def$  
cluster input name "@words.tsv", headers 0: words
```

That’s all it takes.

Sheerpower reads the TSV file, maps each column into the declared variables (`word$`, `def$`), and builds a high-performance in-memory structure you can query instantly.

## 2. Processing an HTMX Request

```
word = http_get("word")  
  
if words::exists(word)  
    def = words::lookup(word)  
else  
    def = "No exact match found."  
endif  
  
html = "<div class='result'><b>" & word &  
       "</b><br>" & def & "</div>"  
  
http_response(html)
```

Clear intent.  
Zero ceremony.  
The runtime optimizes everything behind the scenes.

## HTMX: HTML Superpowers Without the Weight

HTMX is not a framework.  
It’s HTML with extras.

You get:

- `hx-get` → fetch HTML
- `hx-post` → submit without reload
- `hx-target` → choose DOM region
- `hx-trigger` → user events
- `hx-swap` → how DOM is replaced

## HTMX Search Form

```
<form hx-get="/scripts/spiis.dll/word/get_def.html"  
      hx-target="#definition"  
      hx-trigger="submit">  
  <input name="word" placeholder="Search..." required>  
</form>  
  
<div id="definition">Type a word to begin.</div>
```

100% declarative.  
100% readable.

## Diagram: Simplicity Comparison

```
Traditional SPA                 HTMX + Sheerpower  
 ───────────────────────        ───────────────────────────  
 JS Framework                   No Framework  
 JSON APIs                      No JSON  
 State Management               No Client State Machines  
 Hydration                      No Hydration  
 Bundlers                       No Build Step  
 Client Router                  Browser Native Navigation
```

Sometimes less really is more.

## The Architecture Pattern That Just Works

1. User types
2. HTMX sends small request
3. Sheerpower looks up data in memory
4. Responds with a tiny HTML fragment
5. Browser swaps it

Feels instant.  
Feels clean.  
Feels like how the web is _supposed_ to work.

## Where This Shines

- Admin tools
- CRUD apps
- Search interfaces
- Internal dashboards
- Reports
- Dictionaries / autocomplete
- Any “boring business UI”

## 🔹 Sheerpower supports offline-first applications extremely well.

- The runtime is fully memory-resident
- TSV/config/data files are preloaded
- Server logic doesn’t depend on remote APIs
- Everything works without network connectivity

These are the apps that keep companies running — and don’t need React + GraphQL + Vite + 9 layers of tooling.

## 🔹 HTMX can also work offline

Simply load HTMX locally:

```
<script src="/static/js/htmx.min.js"></script>
```

As long as your HTML, JS, and Sheerpower runtime are local, the entire system functions offline.

### Where This Doesn’t Fit

- Collaborative realtime apps
- Canvas-heavy editors
- Apps needing giant client-side state

Everything else?  
This model is more than enough.

## Getting Started

1. Begin with pure HTML.
2. Add HTMX attributes.
3. Write a simple Sheerpower handler.
4. Load your data once.
5. Return HTML fragments.
6. Ship.

You’ll feel the difference immediately.

## Explore Further

Try a live example here:

[**https://learnsheerpower.ttinet.com/scripts/spiis.dll/sp_online-api/code_tester?shortname=htmx_fastcgi_dictionary**](https://learnsheerpower.ttinet.com/scripts/spiis.dll/sp_online-api/code_tester?shortname=htmx_fastcgi_dictionary)

## Final Thought

HTML works.  
Browsers are excellent at rendering it.  
Servers are excellent at generating it.

**Sheerpower + HTMX isn’t “old school.”  
It’s the timeless path we forgot.**

If you’re tired of unnecessary complexity, try this duo.  
You might fall in love with the web again.