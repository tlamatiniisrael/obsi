
_Every senior engineer remembers the moment it clicked system design is not about knowing the right answer. It’s about knowing which tradeoff to make, and why._

Interviewers at top companies are not testing memorization. They’re watching whether you can reason through competing concerns under pressure. These seven tradeoffs separate candidates who get offers at L4 from those who land L6 and L7 roles.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*JnZ5XomsBm4wQku0)

Ai Generated Image

### 1. Vertical vs Horizontal Scaling

**What they mean:**

Vertical scaling means adding more power to a single machine more CPU, more RAM, faster disk. Horizontal scaling means adding more machines and distributing the load across them.

```
Vertical Scaling:  
[Server: 8 CPU, 16GB RAM]  →  [Server: 64 CPU, 256GB RAM]  
  
Horizontal Scaling:  
[Server A]  
[Server B]  ← Load Balancer ← Users  
[Server C]
```

**Vertical Pros & Cons:**

- Simple to implement, no code changes needed
- No network latency between nodes
- Hard ceiling: you cannot scale beyond the biggest machine available
- Single point of failure

**Horizontal Pros & Cons:**

- Virtually unlimited scale
- Fault tolerant by design
- Requires stateless application design, a load balancer, and session management complexity

**When to choose what:**

Choose vertical for early-stage products, databases under moderate load, or legacy systems that cannot be distributed easily. Choose horizontal when you need high availability, expect traffic spikes, or your service needs to survive individual node failures. Most production systems at scale end up horizontal but many start vertical because it’s faster to ship.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*PRl8gwbAwH1rwxtZ)

### 2. Concurrency vs Parallelism

**What they mean:**

Concurrency is about dealing with multiple tasks at the same time not necessarily executing them simultaneously. Parallelism is about executing multiple tasks at the exact same instant, using multiple CPU cores.

``` python
# Concurrency — one thread, switching context  
import asyncio  
  
async def fetch_data(url):  
    await asyncio.sleep(1)  # non-blocking I/O wait  
    return f"data from {url}"  
  
async def main():  
    results = await asyncio.gather(  
        fetch_data("api/users"),  
        fetch_data("api/orders"),  
    )  
  
# Parallelism - multiple threads/processes  
from concurrent.futures import ProcessPoolExecutor  
  
def cpu_intensive(n):  
    return sum(i * i for i in range(n))  
with ProcessPoolExecutor() as executor:  
    results = list(executor.map(cpu_intensive, [10**6, 10**6, 10**6]))
```

**Concurrency Pros & Cons:**

- Excellent for I/O-bound tasks (network calls, DB queries, file reads)
- Low memory overhead with async models
- Does not help with CPU-bound work

**Parallelism Pros & Cons:**

- True speedup for CPU-bound computation
- Higher memory consumption
- Risk of race conditions when shared state is involved

**When to choose what:**

Use concurrency for web servers, API gateways, and anything waiting on network or disk. Use parallelism for image processing, data crunching, ML inference, or any computation that saturates a single core.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*FHDD2t5P_vJnwI3c)

Ai Generated Image

### 3. Push vs Pull Architecture

**What they mean:**

In a push model, the server sends data to clients as soon as it’s available. In a pull model, clients periodically ask the server for new data.

```
Push:  
  Server ──────────────────► Client A  
         └────────────────► Client B  
  
Pull:  
  Client A ──► "any updates?" ──► Server ──► response  
  Client B ──► "any updates?" ──► Server ──► response  
  (every N seconds)
```

**Push Pros & Cons:**

- Low latency delivery
- Efficient when events are frequent
- Server must manage connections and track all subscribers
- Harder to implement backpressure

**Pull Pros & Cons:**

- Simple to implement and debug
- Client controls its own consumption rate
- Wasteful when updates are rare (polling overhead)
- Inherent latency equal to polling interval

**When to choose what:**

Push suits real-time notifications, live dashboards, and chat applications. Pull works well for batch jobs, periodic sync, and situations where slight delay is acceptable. Many mature systems combine both Kafka consumers pull messages, but the system pushes events into Kafka.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*7wz4QDkjj7ZkXuUV)

Ai Generated Image

### 4. Stateful vs Stateless Architecture

**What they mean:**

A stateless service holds no client session data between requests. Every request carries all the context needed. A stateful service remembers previous interactions.

```
Stateless (JWT-based):  
  Request → [JWT token contains user context] → Any server can handle it  
    
  [Server A]  
  [Server B]  ← All identical, request routed to any  
  [Server C]  
  
Stateful (Session-based):  
  User session stored on Server A  
  → Future requests MUST go to Server A (sticky sessions)
```

**Stateless Pros & Cons:**

- Trivially horizontally scalable
- Easy to deploy and restart without data loss
- Tokens can become large if they carry too much context
- Cannot easily invalidate sessions server-side

**Stateful Pros & Cons:**

- Rich session context, fast lookups
- Easy to revoke access
- Sticky routing complicates load balancing
- Node failure loses session data unless externalized

**When to choose what:**

Default to stateless for REST APIs and microservices. Use stateful when you need complex session context, real-time multiplayer state, or financial transaction sequences where continuity matters. If you must go stateful, externalize state to Redis or a distributed cache so nodes remain interchangeable.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*4TQOzBYYdhz2KcIY)

Ai Generated Image

### 5. Long Polling vs WebSockets

**What they mean:**

Long polling is HTTP-based: the client sends a request, the server holds it open until data is available, then responds. The client immediately sends a new request. WebSockets establish a persistent, full-duplex TCP connection.

```
Long Polling:  
  Client ──── Request ────► Server (holds connection open)  
  Client ◄─── Response ─── Server (when data is ready)  
  Client ──── Request ────► Server (immediately re-connects)  
  
WebSocket:  
  Client ◄══════════════► Server (persistent bidirectional pipe)  
  Client ◄── message ───  Server  
  Client ─── message ──► Server
```

``` js
// WebSocket server (Node.js)  
const WebSocket = require('ws');  
const wss = new WebSocket.Server({ port: 8080 });  
  
wss.on('connection', (ws) => {  
  ws.on('message', (msg) => {  
    // broadcast to all clients  
    wss.clients.forEach(client => {  
      if (client.readyState === WebSocket.OPEN) {  
        client.send(msg);  
      }  
    });  
  });  
});
```

**Long Polling Pros & Cons:**

- Works everywhere HTTP works, no special infrastructure
- Simpler fallback for restricted networks
- Higher latency per message
- More HTTP overhead and connection churn

**WebSockets Pros & Cons:**

- True real-time, sub-millisecond delivery
- Full-duplex, low overhead after handshake
- Requires persistent connection management at scale
- Load balancers need sticky session or shared pub/sub

**When to choose what:**

Use WebSockets for chat apps, multiplayer games, live trading platforms, and collaborative editing tools. Use long polling when WebSocket infrastructure is unavailable, or as a graceful fallback. Most modern systems pair WebSockets with a message broker like Redis Pub/Sub to fan out messages across multiple server instances.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*JJ6q5ZdZsRwgh5Go)

Ai Generated Image

### 6. Strong vs Eventual Consistency

**What they mean:**

Strong consistency guarantees that after a write, any subsequent read from any node returns the updated value. Eventual consistency allows temporary divergence; all nodes will converge to the same value, but not immediately.

```
Strong Consistency:  
  Write "balance = $500" → All replicas immediately reflect $500  
  Any read → $500 (guaranteed)  
  
Eventual Consistency:  
  Write "balance = $500" → Primary updated  
  Read from Replica A → might return $450 (stale)  
  Read from Replica A (after sync) → $500 ✓
```

This connects directly to the **CAP theorem**: in a distributed system, during a network partition you must choose either Consistency or Availability.

**Strong Consistency Pros & Cons:**

- Predictable behavior, no stale reads
- Essential for financial and inventory systems
- Higher latency (coordination overhead between nodes)
- Lower availability during partitions

**Eventual Consistency Pros & Cons:**

- High availability and low latency
- Systems stay responsive during network issues
- Application layer must handle conflicts and stale data
- Harder to reason about correctness

**When to choose what:**

Choose strong consistency for bank balances, seat reservations, inventory deductions, and any domain where a stale read causes real-world harm. Choose eventual consistency for social media feeds, user profile views, analytics counters, and systems where brief inconsistency is tolerable. DynamoDB defaults to eventual but offers strongly consistent reads at extra cost that flexibility is something interviewers love to discuss.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*-vlS40osVOxhDC1U)

Ai Generated Image

### 7. Putting It Together The Interview Mindset

The real signal interviewers look for is not whether you pick vertical or horizontal, push or pull, strong or eventual. It is whether you can articulate the forces pulling in each direction and make a defensible choice given the constraints of the problem.

A practical framework:

```
1. What are the scale requirements? (users, RPS, data volume)  
2. What are the latency requirements?  
3. What fails if the system is temporarily inconsistent?  
4. What is the cost of failure vs. the cost of complexity?  
5. What does the team have the capacity to operate?
```

L4 engineers pick an approach and implement it. L5 engineers can compare two options. L6 engineers proactively surface tradeoffs the interviewer hasn’t asked about yet. L7 engineers frame the entire design around business risk and organizational constraints before touching architecture.

The tradeoffs in this article are not exam questions , they are lenses. Each one, applied at the right moment in a system design discussion, signals the depth of thinking that separates a senior engineer from an architect.

_Found this useful? Drop a comment with the tradeoff that trips you up most in interviews happy to dig deeper on any of these._

[System Design Interview](https://medium.com/tag/system-design-interview?source=post_page-----441cd13fae4d---------------------------------------)

[Design Systems](https://medium.com/tag/design-systems?source=post_page-----441cd13fae4d---------------------------------------)

[System Design Concepts](https://medium.com/tag/system-design-concepts?source=post_page-----441cd13fae4d---------------------------------------)

[Software Engineering](https://medium.com/tag/software-engineering?source=post_page-----441cd13fae4d---------------------------------------)

[Coding Interviews](https://medium.com/tag/coding-interviews?source=post_page-----441cd13fae4d---------------------------------------)

123

2

[![The Latency Gambler](https://miro.medium.com/v2/resize:fill:96:96/1*wMFzQ6KVGegm1kaMnFxANw.jpeg)](https://medium.com/@kanishks772?source=post_page---post_author_info--441cd13fae4d---------------------------------------)

[## Written by The Latency Gambler](https://medium.com/@kanishks772?source=post_page---post_author_info--441cd13fae4d---------------------------------------)

[20K followers](https://medium.com/@kanishks772/followers?source=post_page---post_author_info--441cd13fae4d---------------------------------------)

·[1 following](https://medium.com/@kanishks772/following?source=post_page---post_author_info--441cd13fae4d---------------------------------------)

Tech critic exploring tools, systems & languages beyond hype. Linkedin-[https://www.linkedin.com/in/kanishk-singh-140059189/](https://www.linkedin.com/in/kanishk-singh-140059189/) Mail id - [kanishks772@gmail.com](mailto:kanishks772@gmail.com)

Follow