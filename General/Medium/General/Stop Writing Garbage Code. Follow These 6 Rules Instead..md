
_Every senior engineer has that one codebase. The one that makes them want to quit._

You open a file and it’s 800 lines long. Functions named `doStuff()`. No comments. No tests. Logic copy-pasted five times across six files. It works barely and nobody dares touch it.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*Pci8yrZU3uJpWsBz)

Ai Generated Image

Here’s the uncomfortable truth: that code didn’t write itself. Someone built it, probably in a hurry, without these six rules in mind.

Let’s fix that.

## 1. SOC Separation of Concerns

Your code should do **one thing per layer**. UI logic should not talk to a database. Business rules should not care about HTTP responses. When concerns bleed into each other, every change breaks something unexpected.

**Messy:**

``` python
def handle_user_signup(request):  
    # validates, saves to DB, sends email — all in one function  
    if not request.data.get("email"):  
        return {"error": "Missing email"}  
    db.execute("INSERT INTO users VALUES (?)", request.data["email"])  
    smtp.send(request.data["email"], "Welcome!")  
    return {"status": "ok"}
``` 

**Clean:**

``` python
# Layer 1: Validation  
def validate_signup(data):  
    if not data.get("email"):  
        raise ValueError("Missing email")  
  
# Layer 2: Repository  
def save_user(email):  
    db.execute("INSERT INTO users VALUES (?)", email)  
  
# Layer 3: Notification  
def send_welcome_email(email):  
    smtp.send(email, "Welcome!")  
  
# Layer 4: Orchestration  
def handle_user_signup(request):  
    validate_signup(request.data)  
    save_user(request.data["email"])  
    send_welcome_email(request.data["email"])  
    return {"status": "ok"}
``` 

``` 
┌─────────────┐     ┌──────────────┐     ┌──────────────┐  
│  Controller │────▶│   Service    │────▶│  Repository  │  
│  (HTTP)     │     │  (Business)  │     │  (Database)  │  
└─────────────┘     └──────────────┘     └──────────────┘  
        │                                       │  
        ▼                                       ▼  
  Handles request                        Handles data  
  parsing only                           storage only
``` 

Each layer has a single job. Change one without touching the others.

## 2. DYC Document Your Code

Comments are not for explaining _what_ the code does that should be obvious from the code itself. They exist to explain _why_.

**Useless comment:**

``` 
// loop through users  
for (const user of users) { ... }
``` 

**Useful comment:**

``` 
// Skipping inactive users here because the billing API  
// throws a 422 on deactivated accounts — confirmed with API team  
for (const user of users.filter(u => u.isActive)) { ... }
``` 

==A good rule of thumb: if you had to think for more than 30 seconds about== ==_why_== ==a piece of code exists, it needs a comment.== Future-you (and your teammates) will thank you.

Also use docstrings for functions with non-obvious contracts:

``` python
def calculate_refund(order_id: str, partial: bool = False) -> float:  
    """  
    Returns the refund amount for an order.  
      
    If partial=True, only refunds items not yet shipped.  
    Full refunds include shipping costs.  
    Raises OrderNotFoundError if order_id is invalid.  
    """
``` 

## 3. DRY Don’t Repeat Yourself

Duplication is the root of most maintenance nightmares. When the same logic exists in three places, a bug fix means three separate changes and someone always misses one.

**Repeated logic:**

``` js
// In checkout.js  
const tax = price * 0.18;  
  
// In invoice.js  
const tax = amount * 0.18;  
  
// In cart.js  
const tax = subtotal * 0.18;
``` 

**DRY version:**

``` js
// utils/tax.js  
export const calculateTax = (amount, rate = 0.18) => amount * rate;  
  
// Used everywhere  
import { calculateTax } from '../utils/tax';  
const tax = calculateTax(price);
``` 

Now if the tax rate changes, there is exactly one place to update.

``` 
BEFORE DRY:                    AFTER DRY:  
  
checkout.js ──┐               checkout.js ──┐  
              │ (same logic)                 ├──▶ calculateTax()  
invoice.js  ──┤               invoice.js  ──┤      (one source)  
              │               cart.js     ──┘  
cart.js     ──┘
``` 

DRY applies to logic, not just literal copy-paste. If two functions follow the same pattern, consider abstracting it.

## 4. KISS Keep It Simple, Stupid

Clever code is often a liability. When you write something that requires 10 minutes to understand, you’ve added 10 minutes of cost every time someone (including you) reads it.

**“Clever”:**

``` python
result = next((x for x in data if x["active"]), {}).get("value", 0)
``` 

**Simple:**

``` python
active_items = [x for x in data if x["active"]]  
result = active_items[0]["value"] if active_items else 0
``` 

Both do the same thing. One is readable at a glance. Guess which one causes a 2am incident.

KISS also applies to architecture. Don’t reach for microservices when a monolith will do. Don’t add a message queue for a feature used by 10 people.

``` 
SIMPLE ARCHITECTURE          OVER-ENGINEERED  
(for a small app):           (for a small app):  
  
Client ──▶ Server ──▶ DB     Client  
                             │  
                             ▼  
                          API Gateway  
                             │  
                      ┌──────┴──────┐  
                      ▼             ▼  
                  Service A     Service B  
                      │             │  
                    Queue         Cache  
                      │             │  
                     DB1           DB2

Build for the problem you have today, not the one you imagine having in three years.
``` 

## 5. TDD Test Driven Development

Writing tests _after_ code is like proofreading after publishing. It happens, but it misses the point.

TDD flips the workflow: write a failing test first, then write just enough code to make it pass, then refactor. The result is code that is inherently testable because it was designed around tests from the start.

``` python
# Step 1: Write the test (it will fail)  
def test_calculate_discount():  
    assert calculate_discount(100, "SAVE10") == 90  
    assert calculate_discount(100, "INVALID") == 100  
  
# Step 2: Write the minimum code to pass  
def calculate_discount(price, code):  
    discounts = {"SAVE10": 0.10}  
    rate = discounts.get(code, 0)  
    return price * (1 - rate)  
  
# Step 3: Refactor confidently - tests have your back
``` 

``` 
TDD CYCLE:  
  
    ┌──────────────┐  
    │  Write Test  │◀─────────────┐  
    └──────┬───────┘              │  
           │                      │  
           ▼                      │  
    ┌──────────────┐              │  
    │  Test Fails  │              │  
    └──────┬───────┘         REFACTOR  
           │                      │  
           ▼                      │  
    ┌──────────────┐              │  
    │  Write Code  │              │  
    └──────┬───────┘              │  
           │                      │  
           ▼                      │  
    ┌──────────────┐              │  
    │  Test Passes │──────────────┘  
    └──────────────┘
``` 

TDD is not about having 100% coverage. It’s about having confidence that the critical paths work and knowing immediately when they break.

## 6. YAGNI You Ain’t Gonna Need It

The most dangerous four words in software are: _“We might need this later.”_

Features built speculatively sit unused, increase complexity, and eventually get deleted after costing weeks of engineering time.

**Violating YAGNI:**

``` java
class UserService {  
  constructor() {  
    this.cache = new RedisCache();        // not needed yet  
    this.analytics = new MixpanelClient(); // not requested  
    this.featureFlags = new LaunchDarkly(); // premature  
  }  
  
getUser(id) { ... }  // the one thing actually needed  
}
``` 

**YAGNI-compliant:**

``` java
class UserService {  
  getUser(id) {  
    return db.users.findById(id);  
  }  
}  
// Add complexity when the requirement exists, not before
``` 

YAGNI doesn’t mean writing throwaway code. It means not solving problems that don’t exist yet. When the requirement arrives, you’ll have better context to solve it properly anyway.

## Putting It All Together

These six rules aren’t independent they reinforce each other.

``` 
┌────────────────────────────────────────────────┐  
│              CLEAN CODE FOUNDATION             │  
├──────────┬──────────┬──────────┬───────────────┤  
│  SOC     │   DRY    │  KISS    │     TDD       │  
│ (layers) │ (reuse)  │(simple)  │  (safety net) │  
├──────────┴──────────┴──────────┴───────────────┤  
│         DYC (document the why)                 │  
│         YAGNI (build what's needed)            │  
└────────────────────────────────────────────────┘
``` 

A codebase following these rules doesn’t feel heroic. It feels boring and that’s exactly the point. Boring code ships reliably, onboards new engineers quickly, and doesn’t page you at 3am.

The best engineers aren’t the ones who write the most complex code. They’re the ones whose code requires the least explanation.

_Start with one rule. Apply it consistently for a week. Then add another. These aren’t ceremonies they’re habits._

[Clean Code](https://medium.com/tag/clean-code?source=post_page-----f2cc6e178258---------------------------------------)

[Software Engineering](https://medium.com/tag/software-engineering?source=post_page-----f2cc6e178258---------------------------------------)

[System Design Concepts](https://medium.com/tag/system-design-concepts?source=post_page-----f2cc6e178258---------------------------------------)

[Software Development](https://medium.com/tag/software-development?source=post_page-----f2cc6e178258---------------------------------------)

[Programming](https://medium.com/tag/programming?source=post_page-----f2cc6e178258---------------------------------------)

113

3

[![The Latency Gambler](https://miro.medium.com/v2/resize:fill:96:96/1*wMFzQ6KVGegm1kaMnFxANw.jpeg)](https://medium.com/@kanishks772?source=post_page---post_author_info--f2cc6e178258---------------------------------------)

[## Written by The Latency Gambler](https://medium.com/@kanishks772?source=post_page---post_author_info--f2cc6e178258---------------------------------------)

[20K followers](https://medium.com/@kanishks772/followers?source=post_page---post_author_info--f2cc6e178258---------------------------------------)

·[1 following](https://medium.com/@kanishks772/following?source=post_page---post_author_info--f2cc6e178258---------------------------------------)

Tech critic exploring tools, systems & languages beyond hype. Linkedin-[https://www.linkedin.com/in/kanishk-singh-140059189/](https://www.linkedin.com/in/kanishk-singh-140059189/) Mail id - [kanishks772@gmail.com](mailto:kanishks772@gmail.com)

Follow