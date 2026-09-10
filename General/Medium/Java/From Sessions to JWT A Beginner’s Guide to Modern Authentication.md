
## From Sessions to JWT: A Beginner’s Guide to Modern Authentication

[

![Kalyani Dagde](https://miro.medium.com/v2/da:true/resize:fill:64:64/0*TSa_8Vyj2P14JBuN)



](https://medium.com/@dagde.kalyani?source=post_page---byline--3f5a79689544---------------------------------------)

Having worked in the banking domain for more than five years, I’ve gained a deep appreciation for the security measures these organisations implement and the problems they are designed to solve. In highly regulated environments, security is not just a technical requirement, it is foundational to trust, compliance, and business continuity.

However, when I first entered this domain, many of the terms and concepts surrounding security felt overwhelming. I had heard the jargon before- authentication, authorization, tokens, OAuth, public keys but understanding them in theory is very different from applying them in real systems. In team discussions, architect reviews, and production troubleshooting, those familiar words suddenly felt abstract and confusing.

Recently, while interviewing a junior developer, I noticed that same uncertainty. The candidate had encountered the terminology, but there was hesitation when connecting concepts to practical scenarios. It reminded me of my early days- knowing the vocabulary but not fully grasping the underlying principles.

> Not a member? [Read Here.](https://medium.com/code-like-a-girl/security-concepts-every-java-developer-in-banking-should-master-part-1-3f5a79689544?sk=66b763de48d8494e1d64501fe467fc7a)

That realization inspired this article.

In this series, we will break down the foundational concepts of application security in a clear, structured, and practical way. The goal is to remove the confusion around commonly used terms and build a strong conceptual base. In this first part, we will focus only on the fundamentals, understanding the core terminology and the problems these mechanisms are designed to solve. In the upcoming parts, we will go deeper into implementation details and real-world applications.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:700/0*Jo9lPqAf1unJp55U)

Photo by [FlyD](https://unsplash.com/@flyd2069?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Let’s start with the most common confusion in application security:

### **Authentication vs Authorization**

These two words are often used together. Many developers use them interchangeably. But they solve two very different problems.

Authentication answers one simple question: _Who are you?_

When a user enters their username and password, the system verifies whether those credentials are valid. If they are correct, the user is authenticated.

Authorization comes after authentication.

It answers: _Now that I know who you are, what are you allowed to do?_

For example:

- A normal user can view their profile.
- An admin can delete users.

Everything in Spring Security-filters, tokens, and roles revolves around these two ideas.

But once a user is authenticated, another question arises.

### **Identity — What Does It Really Mean?**

Identity is simply the representation of the user inside the system.

It may include:

- User ID
- Username
- Roles
- Permissions

Once the system authenticates a user, it now knows their identity. But identity alone is not enough. Because web applications are stateless by nature. Every HTTP request is independent. So how does the system remember who you are on the next request?

That’s where **sessions and tokens** come in.

### **Sessions**

Traditionally, applications used sessions to manage authentication. After login, the server created a session in memory and stored the user’s identity. A session ID was returned to the client, typically via a cookie. On each subsequent request, the client sent the session ID back, and the server retrieved the stored identity.

Because the server maintains state across requests, this is called stateful authentication. For single-server applications, this model worked reliably and was simple to implement.

**The Problem with Sessions at Scale**

As systems evolved into microservices and began running across multiple servers, sessions became harder to manage. If a user logged in on one server but their next request was routed to another server, that second server would not recognise the session.

To solve this, teams introduced sticky sessions or centralised session storage using tools like Redis. While effective, these solutions increased infrastructure complexity, added network overhead, and introduced operational risk.

In distributed systems, making the server responsible for remembering users became fragile.

Modern systems need a different approach — one where the server does not store user state, and instead, each request carries proof of identity.

This shift led to the adoption of tokens.

### **Token**

Instead of storing identity in server memory, the server issues a token after authentication. The client sends this token with every request. The server verifies the token and extracts the identity from it.

No session storage.  
No shared memory.  
No sticky routing.

This approach is called stateless authentication.

Now we can ask the next important question:

Where does JWT come into the picture?

### JWT- JSON Web Token

JWT is a compact, self-contained token format used to securely transmit information between a client and a server.

After a user successfully authenticates (for example, by logging in), the server generates a JWT and sends it to the client. The client then includes this token in every subsequent request. The server verifies the token and, if valid, trusts the identity and permissions inside it.

This enables **stateless authentication**, which works well in modern distributed systems.

A JWT has three parts separated by dots:

header.payload.signature

**Header**

Specifies the token type and signing algorithm (e.g., HS256 or RS256).

**Payload**

Contains claims such as:

- Subject (sub)
- Roles
- Expiration time (exp)

The payload is Base64 encoded, not encrypted. Sensitive data should never be stored inside it.

**Signature**

The signature ensures integrity. It is created by signing the header and payload using either:

- A shared secret (symmetric), or
- A private key (asymmetric).

When the server recalculates the signature, and it matches, the token is trusted. If the token is modified, the signature fails validation.

### Common Actors in the Security World

When implementing security with OAuth2 and JWT, several core participants are involved.

1. **User-** also called the Resource Owner, is the person who owns the data.

**2. Client-** is the application the user interacts with — a web app, mobile app, or backend service.

**3. Authorization Server-** authenticates users and issues tokens. Examples include Keycloak, Auth0, Azure AD, and Okta.

**4. Resource Server-** hosts protected APIs and validates access tokens.

**5. Identity Provider (IdP)-** verifies user identity. In many systems, the Authorization Server also acts as the IdP. OpenID Connect (OIDC) extends OAuth2 to provide identity information.

**6. Access Token-** often a JWT, is the credential used to access protected resources.

These are the fundamental security concepts every backend developer should clearly understand before moving into advanced frameworks or configurations. A strong foundation makes everything else easier.

I’d love to know which concept became clearer for you today, and if there’s something important you think I missed, share it in the comments.

If you found this article helpful, please give it a clap; it truly motivates me to keep writing and simplifying complex topics.

You can read my other articles here :