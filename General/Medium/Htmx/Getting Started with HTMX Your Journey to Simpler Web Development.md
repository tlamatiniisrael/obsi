
# Getting Started with HTMX: Your Journey to Simpler Web Development

![](https://miro.medium.com/v2/resize:fit:1400/1*HglWBaB8ipjDt3lpNu5CQA.png)

Remember the last time you needed to add a simple interactive feature to your web page? Maybe it was a “Load More” button, or a form that submits without refreshing the page. You probably reached for React, Vue, or Angular; frameworks that require build tools, state management, and hundreds of kilobytes of JavaScript just to handle what should be a straightforward task.

What if I told you there’s a better way? A way that lets you build dynamic, modern web applications using the tools you already know: HTML and your server-side language of choice.

Welcome to HTMX.

## The Problem We’re Solving

Let’s start with a story you’ve probably lived. You’re building a simple blog, and the product manager says, “Hey, can we make the comments section load dynamically? Users shouldn’t have to refresh the whole page.”

In the traditional JavaScript framework world, this seemingly simple request spirals into:

- Setting up a build pipeline
- Installing hundreds of npm packages
- Learning state management patterns
- Writing API endpoints that return JSON
- Creating components that transform that JSON back into HTML
- Debugging why your bundle size just hit 500KB

By the time you’re done, you’ve spent three days on what should have been a 30-minute task. And worse? You’ve created a maintenance burden that will haunt your team for years.

There has to be a better way. And there is.

## Enter HTMX: HTML on Steroids

HTMX is built on a simple but powerful idea: **what if HTML itself could handle modern web interactions?**

Think about it. HTML already knows how to make requests to servers — that’s what forms and links do. The problem is, it only knows two tricks:

- Links (`<a>`) make GET requests and replace the entire page
- Forms (`<form>`) make GET or POST requests and replace the entire page

HTMX asks: “What if we could teach HTML more tricks?” What if any element could:

- Make any type of HTTP request (GET, POST, PUT, DELETE, PATCH)
- Be triggered by any event (click, hover, scroll, custom events)
- Update any part of the page, not just replace everything
- Handle common UI patterns with simple attributes

Let’s see what this looks like in practice.

## Your First HTMX Feature: A “Load More” Button

Imagine you’re building a blog with paginated posts. Users want to load more posts without refreshing the page. Here’s how you’d do it with HTMX:

```html
<div id="posts-container">  
  <!-- Your initial posts here -->  
  <article>First post...</article>  
  <article>Second post...</article>  
  <article>Third post...</article>  
</div>
```

```html
<button   
  hx-get="/posts?page=2"  
  hx-target="#posts-container"  
  hx-swap="beforeend">  
  Load More Posts  
</button><script src="https://unpkg.com/htmx.org@1.9.10"></script>
```

That’s it. Seriously. Let me walk you through what’s happening:

1. **hx-get="/posts?page=2"** - When clicked, make a GET request to this URL
2. **hx-target="#posts-container"** - Take the response and put it in this element
3. **hx-swap="beforeend"** - Specifically, append it to the end of the container

Your server just returns HTML fragments — the exact same HTML you’d render on the initial page load:

```html
<article>Fourth post...</article>  
<article>Fifth post...</article>  
<article>Sixth post...</article>
```

No JSON. No client-side templating. No state management. Just HTML going back and forth, the way the web was meant to work.

## The “Aha!” Moment: It’s Just Hypermedia

Here’s where HTMX clicks for most people: **you’re already doing this**. Every time you render a page on your server, you’re generating HTML. HTMX just lets you send those same HTML fragments to update parts of the page instead of replacing the whole thing.

Your mental model doesn’t change. Your tools don’t change. Your server-side rendering logic doesn’t change. You just get to use it for dynamic updates too.

This is the power of hypermedia — using HTML as your data format for everything, not just initial page loads.

## Building Something Real: A Search Feature

Let’s level up with a more realistic example. You want to add live search to your blog — as users type, results appear below the search box.

```html
<div>  
  <input   
    type="search"   
    name="q"  
    placeholder="Search posts..."  
    hx-get="/search"  
    hx-trigger="keyup changed delay:500ms"  
    hx-target="#search-results"  
    hx-indicator="#spinner">  
    
  <img id="spinner" class="htmx-indicator" src="/spinner.gif"/>  
    
  <div id="search-results"></div>  
</div>
```

Let’s break down the magic happening here:

`**hx-trigger="keyup changed delay:500ms"**` - This is where HTMX shines. It says:

- Listen for `keyup` events
- Only trigger if the value actually `changed`
- Wait `500ms` after the user stops typing before making the request

This simple attribute handles debouncing, change detection, and event management — things that would normally require a JavaScript utility library or custom code.

`**hx-indicator="#spinner"**` - Automatically show/hide a loading indicator. HTMX adds a class when the request is in flight, and removes it when done. No manual state management needed.

On your server, you just return the search results as HTML:

```html
<div class="search-results">  
  <article>  
    <h3>Matching Post Title</h3>  
    <p>Preview of the matching content...</p>  
  </article>  
  <!-- more results -->  
</div>
```

## The Power of Progressive Enhancement

Here’s something beautiful about this approach: **your site works without JavaScript**.

Take that search form. If JavaScript fails to load (it happens more than you think — mobile networks, ad blockers, corporate firewalls), users can still submit the form the traditional way. HTMX gracefully degrades to standard HTML behavior.

Try this yourself: disable JavaScript and click an HTMX-powered link. It still works, just with a full page refresh instead of a smooth update. Your users never hit a broken page.

This is progressive enhancement in action — starting with solid HTML foundations and layering on enhancements.

## Common Patterns You’ll Use Every Day

Once you understand the basics, you’ll find yourself reaching for these patterns constantly:

## Inline Editing

```html
<div hx-target="this" hx-swap="outerHTML">  
  <div>  
    <label>Email:</label> user@example.com  
    <button hx-get="/contacts/1/edit">Edit</button>  
  </div>  
</div>
```

When clicked, the server returns an edit form that replaces this div. When saved, it returns the updated display view. The entire edit/save cycle happens in place, no page navigation needed.

## Infinite Scroll

```html
<div hx-get="/posts?page=2"   
     hx-trigger="revealed"  
     hx-swap="afterend">  
  <p>Loading more posts...</p>  
</div>
```

The `revealed` trigger fires when the element scrolls into view. Perfect for infinite scroll with zero JavaScript.

## Delete with Confirmation

```html
<button hx-delete="/contacts/1"  
        hx-confirm="Are you sure you want to delete this contact?"  
        hx-target="closest .contact-row"  
        hx-swap="outerHTML swap:1s">  
  Delete  
</button>
```

Built-in confirmation dialogs and smooth transitions. The `swap:1s` adds a one-second fade-out before removing the element.

## Understanding HTMX Attributes: Your New Vocabulary

HTMX uses HTML attributes to configure behavior. Here are the core ones you’ll use:

## Request Attributes

- `**hx-get**`**,** `**hx-post**`**,** `**hx-put**`**,** `**hx-delete**`**,** `**hx-patch**` - Make HTTP requests
- `**hx-trigger**` - What event triggers the request (click, submit, custom events)
- `**hx-include**` - Additional elements to include in the request

## Response Attributes

- `**hx-target**` - Where to put the response (CSS selector)
- `**hx-swap**` - How to insert it (`innerHTML`, `outerHTML`, `beforebegin`, `afterend`, etc.)
- `**hx-select**` - Pick specific parts of the response

## Enhancement Attributes

- `**hx-indicator**` - Show loading states
- `**hx-confirm**` - Confirmation dialogs
- `**hx-push-url**` - Update the browser URL
- `**hx-boost**` - Enhance normal links and forms

## When Things Go Wrong: Debugging HTMX

HTMX is remarkably straightforward, but when something doesn’t work, here’s how to debug:

## Enable Logging

```js
<script>  htmx.logAll();</script>
```

This logs every HTMX action to the console. You’ll see exactly what requests are being made and what’s happening with the responses.

## Check Your Server Response

The most common issue? Your server isn’t returning what you expect. Open your browser’s Network tab and look at the response. Is it HTML? Is it the right HTML?

## Watch for CORS Issues

If you’re making cross-origin requests, remember that HTMX follows standard browser security. Your server needs proper CORS headers.

## Verify Your Selectors

If content isn’t appearing where you expect, check your `hx-target` selector. Open the console and try `document.querySelector('#your-target')` to verify it finds the right element.

## The Server Side: What Changes?

The beautiful thing about HTMX is how little your server needs to change. You’re already rendering HTML templates. Now you just need to:

1. **Create endpoints that return HTML fragments** instead of full pages
2. **Detect HTMX requests** (HTMX sends an `HX-Request` header)
3. **Return partial templates** when it’s an HTMX request

Here’s what this looks like in practice. Let’s say you’re using Flask (but this pattern works in any framework):

```python
from flask import Flask, render_template, request

app = Flask(__name__)@app.route('/contacts')  
def contacts():  
    contacts = get_contacts()  
      
    # Check if this is an HTMX request  
    if request.headers.get('HX-Request'):  
        # Return just the contact list fragment  
        return render_template('contacts_list.html', contacts=contacts)  
    else:  
        # Return the full page  
        return render_template('contacts.html', contacts=contacts)
```

Your `contacts.html` template is the full page:

```html
<!DOCTYPE html>  
<html>  
<head>  
    <title>Contacts</title>  
    <script src="https://unpkg.com/htmx.org@1.9.10"></script>  
</head>  
<body>  
    <h1>My Contacts</h1>  
    {% include 'contacts_list.html' %}  
</body>  
</html>
```

And `contacts_list.html` is the fragment you can reuse:

```html
<div id="contacts">  
    {% for contact in contacts %}  
    <div class="contact">  
        <h3>{{ contact.name }}</h3>  
        <p>{{ contact.email }}</p>  
        <button hx-delete="/contacts/{{ contact.id }}"  
                hx-target="closest .contact"  
                hx-swap="outerHTML">  
            Delete  
        </button>  
    </div>  
    {% endfor %}  
</div>
```

Notice how the same template fragment works for both the initial page load AND HTMX updates? That’s the beauty of this approach — no duplication, no separate API layer.

## Real-World Example: Building a Todo App

Let’s put everything together with a complete example. We’ll build a simple todo app that demonstrates the key HTMX patterns.

## The HTML Structure

```html
<!DOCTYPE html>  
<html>  
<head>  
    <title>Todo App</title>  
    <script src="https://unpkg.com/htmx.org@1.9.10"></script>  
    <style>        .todo { padding: 10px; border-bottom: 1px solid #eee; }  
        .todo.completed { opacity: 0.6; text-decoration: line-through; }  
        .htmx-swapping { opacity: 0; transition: opacity 0.3s; }    </style>  
</head>  
<body>  
    <h1>My Todos</h1>  
      
    <!-- Add new todo form -->  
    <form hx-post="/todos"   
          hx-target="#todo-list"   
          hx-swap="afterbegin"  
          hx-on::after-request="this.reset()">  
        <input type="text" name="title" placeholder="What needs to be done?" required>  
        <button type="submit">Add</button>  
    </form>  
      
    <!-- Todo list -->  
    <div id="todo-list">  
        {% for todo in todos %}  
        {% include 'todo_item.html' %}  
        {% endfor %}  
    </div>  
</body>  
</html>
```

## The Todo Item Template

```html
<div class="todo {% if todo.completed %}completed{% endif %}" id="todo-{{ todo.id }}">  
    <input type="checkbox"   
           hx-post="/todos/{{ todo.id }}/toggle"  
           hx-target="#todo-{{ todo.id }}"  
           hx-swap="outerHTML"  
           {% if todo.completed %}checked{% endif %}>  
      
    <span>{{ todo.title }}</span>  
      
    <button hx-delete="/todos/{{ todo.id }}"  
            hx-target="#todo-{{ todo.id }}"  
            hx-swap="outerHTML swap:0.3s"  
            hx-confirm="Delete this todo?">  
        Delete  
    </button>  
</div>
```

## The Server Code

```python
@app.route('/todos', methods=['POST'])  
def add_todo():  
    title = request.form['title']  
    todo = create_todo(title)  
    return render_template('todo_item.html', todo=todo)

@app.route('/todos/<int:id>/toggle', methods=['POST'])  
def toggle_todo(id):  
    todo = get_todo(id)  
    todo.completed = not todo.completed  
    save_todo(todo)  
    return render_template('todo_item.html', todo=todo)@app.route('/todos/<int:id>', methods=['DELETE'])  
def delete_todo(id):  
    delete_todo(id)  
    return '', 200  # Return empty response
```

Look at what we’ve built with barely any JavaScript:

- Add todos without page refresh
- Toggle completion status in place
- Delete with confirmation and smooth animation
- All with progressive enhancement — it works without JavaScript too!

## Advanced Patterns: Taking It Further

Once you’re comfortable with the basics, HTMX offers powerful advanced features:

## Out-of-Band Swaps

Sometimes you need to update multiple parts of the page from a single request. HTMX handles this elegantly:

<!-- Main content -->  
<div id="main-content" hx-get="/dashboard" hx-trigger="every 30s">  
    Dashboard content here...  
</div>

<!-- Notification badge that updates from the same request -->  
<div id="notification-count">0</div>

Your server response:

<!-- This goes in #main-content -->  
<div>Updated dashboard content...</div>

<!-- This goes in #notification-count using out-of-band swap -->  
<div id="notification-count" hx-swap-oob="true">5</div>

The `hx-swap-oob="true"` attribute tells HTMX: "This element belongs somewhere else—find the matching ID and update it."

## WebSocket Support

HTMX can work with WebSockets for real-time updates:

<div hx-ws="connect:/chatroom">  
    <div id="chat-messages"></div>  
      
    <form hx-ws="send">  
        <input name="message" placeholder="Type a message...">  
        <button type="submit">Send</button>  
    </form>  
</div>

Messages from the WebSocket automatically update the page. It’s real-time communication without writing WebSocket handling code.

## Server-Sent Events

For one-way real-time updates (like notifications or live feeds):

<div hx-sse="connect:/notifications">  
    <div hx-sse="swap:notification-event"></div>  
</div>

Your server sends events, and HTMX automatically swaps them into the page.

## The Philosophy: Locality of Behaviour

Here’s something subtle but important about HTMX: it keeps behavior close to the elements it affects. When you see `hx-get="/posts"` on a button, you immediately know what that button does. You don't need to hunt through separate JavaScript files to find event handlers.

This is called “locality of behavior,” and it’s a game-changer for maintainability. Six months from now, when you need to modify how that button works, all the relevant code is right there in the HTML.

Compare this to a typical React app where:

- The button is in one component file
- The click handler is in another
- The state management is in yet another
- The API call is in a service file
- The data transformation is somewhere else

HTMX brings everything back together, making your code easier to understand and maintain.

## Performance: Smaller, Faster, Simpler

Let’s talk numbers. A typical React todo app might ship:

- React library: ~40KB (minified + gzipped)
- React DOM: ~130KB
- Your application code: ~50KB
- **Total: ~220KB** of JavaScript

The HTMX version?

- HTMX library: ~14KB (minified + gzipped)
- Your application code: ~0KB (it’s just HTML attributes)
- **Total: ~14KB**

That’s not a typo. You’re shipping **94% less JavaScript**. Your pages load faster, work on slower devices, and consume less battery.

But it’s not just about file size. With HTMX:

- No build step means faster development
- No virtual DOM means less memory usage
- Server-side rendering means better SEO
- Progressive enhancement means better accessibility

## When HTMX Shines (And When It Doesn’t)

HTMX is perfect for:

- **Content-heavy applications** (blogs, documentation, e-commerce)
- **CRUD applications** (admin panels, dashboards, business tools)
- **Progressive enhancement** scenarios where you need a solid baseline
- **Teams that want to minimize JavaScript complexity**
- **Server-side rendered applications** that need interactivity

HTMX might not be the best choice for:

- **Highly interactive applications** (Figma-like design tools, games)
- **Offline-first applications** (though you can combine HTMX with service workers)
- **Applications with complex client-side state** (though you might be surprised how much you can do)

The key question: “Is my application primarily about displaying and manipulating server data?” If yes, HTMX is probably a great fit.

## Common Questions and Misconceptions

**“Isn’t this just going back to the old way of doing things?”**

Not quite. HTMX gives you the simplicity of traditional server-rendered apps with the interactivity of modern SPAs. You get the best of both worlds.

**“What about SEO?”**

HTMX actually improves SEO because your content is server-rendered. Search engines see fully-formed HTML, not empty divs waiting for JavaScript to run.

**“Can I use HTMX with my existing framework?”**

Absolutely! HTMX works with any server framework: Django, Rails, Laravel, Express, Spring — you name it. It’s just HTML attributes, so it integrates seamlessly.

**“What about accessibility?”**

HTMX maintains proper semantic HTML and keyboard navigation. Plus, server-rendered content is inherently more accessible than client-rendered content.

**“Is it production-ready?”**

Very much so. Companies are using HTMX in production for everything from small internal tools to large-scale public applications. It’s stable, well-tested, and actively maintained.

## Your Next Steps

Ready to start building with HTMX? Here’s your roadmap:

1. **Start Small**: Add HTMX to an existing page. Pick one interactive feature and convert it. See how it feels.
2. **Read the Docs**: The [official HTMX documentation](https://htmx.org/docs/) is excellent. It’s clear, comprehensive, and full of examples.
3. **Join the Community**: The HTMX Discord and GitHub discussions are active and welcoming. Ask questions, share your projects, learn from others.
4. **Build Something**: The best way to learn is by doing. Build a small project — a todo app, a contact manager, a blog with comments. Get hands-on experience.
5. **Explore Extensions**: HTMX has a rich ecosystem of extensions for things like loading states, multi-swap, and more. Explore what’s available.
6. **Share Your Experience**: Write about what you build. The community loves seeing real-world HTMX applications.

_Please note the following article is written using an AI Writer Agent which we are currently testing. The model used is Claude Sonnet 4.5. Have intentionally not made edits on the output. Feedback on the language, strcuture etc are welcome._