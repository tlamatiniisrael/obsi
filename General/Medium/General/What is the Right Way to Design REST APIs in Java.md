


# What is the Right Way to Design REST APIs in Java?


![](https://miro.medium.com/v2/resize:fit:1400/1*gGArhv3X4EgugI6dPzK_Eg.png)

REST stands for **Representational State Transfer**. It is not a protocol but a web-based architecture to design REST APIs. **_APIs define the contract for how clients interact with our system._**

When we design a well-designed API, it is easy to use, maintain and understand. As APIs evolve, we utilise concepts such as versioning and backwards compatibility to ensure their reliability for the future.

> My articles are free for everyone. Non-members can read this full article for free [**HERE**](https://medium.com/@saquibdev/dc2c3e263004?sk=27b4f952502088554ba68c8f622c204d).

## What are the Key Principles of REST?

### Uniform Interface

- URIs identify resources.
- We use **HTTP** verbs like. GET and POST are used to operate on resources.
- The Messages must be self-descriptive.

### Statelessness

This means that every request should be self-contained, including all necessary information.

### Cacheable

The responses should be declared cacheable explicitly as required to improve performance.

### Layered System

The architecture can have multiple layers, like **authentication**, **caching**, **proxies**, etc.

## Best Practices for Designing REST APIs

### URI Designing and Resource Naming

- Use **Nouns** and avoid Verbs: Examples can be /users is a good endpoint, not /getUsers.
- Use hierarchical paths for related resources.
- Please make sure the **URIs are lowercase, consistent and meaningful** to the functionality that they offer.

**Examples**

```http
GET   /api/v1/users  
GET   /api/v1/users/{id}  
POST  /api/v1/users  
PUT   /api/v1/users/{id}  
DELETE /api/v1/users/{id}  
GET   /api/v1/users/{id}/orders
```

### HTTP Methods & Status Codes

- **GET**: Read
- **POST**: CREATE
- **PUT**: Update/Replace
- **PATCH**: partial update
- **DELETE**: Remove resources

Always make sure to return meaningful HTTP Status Codes:

- **200 OK**: successful read/update
- **201 Created**: resource created
- **204 No Content**: successful delete or no payload
- 4**00 Bad Request, 404 Not Found, 500 Internal Server Error** for errors

### Versioning Strategy

We should version APIs to avoid breaking any clients in case of any changes.

We can use URI versioning or Header versioning as required, though URI versioning is the more common practice.

### Request/Response Formats

- Use **JSON** as the default data format for its ubiquity and ease. Set Content-Type: application/json.
- Use appropriate media types for content negotiation if needed.

### Error Handling & Validation

It is essential to make sure that the APIs provide clear error responses with:

- HTTP status code
- Error code/message
- Additional helpful details (e.g., field-level validation errors)

**Validate requests in Java using:**

- DTOs with validation annotations (@NotBlank, @Email, etc.)
- Convert errors into structured JSON responses.

### Caching & Performance

- Use **HTTP** caching when possible: set headers like Cache-Control, ETag, Expires.
- Apply compression (e.g., gzip), rate-limiting, or asynchronous processing to boost performance.

### Security

- Use **HTTPS** to secure transport.
- Apply authentication and authorization using **OAuth 2.0 or JWT Tokens**.
- Implement CORS, input validation, and rate limiting to protect endpoints.

## Java Frameworks & Standards

### Spring Boot & Spring HATEOAS

- Spring Boot simplifies REST API creation with annotations like @RestController, @GetMapping, etc.
- Spring HATEOAS adds hypermedia-driven responses for better navigation. Use EntityModel and WebMvcLinkBuilder to include links.

```java
@GetMapping("/{id}")  
public EntityModel<User> getUser(@PathVariable Long id) {  
    User user = userService.findById(id);  
    return EntityModel.of(user,  
      linkTo(methodOn(UserController.class).getUser(id)).withSelfRel(),  
      linkTo(methodOn(UserController.class).getAllUsers()).withRel("users")  
    );  
}
```

### Jakarta RESTful Web Services (JAX-RS)

- JAX-RS (formerly Java API for RESTful Web Services) is the official standard for REST in Jakarta EE. It uses annotations like [@Path](http://twitter.com/Path), [@GET](http://twitter.com/GET), @POST, @Produces, @ Consumes
- Popular implementations are: Jersey, RESTEasy, Apache CXF (also supports JAX-RS)

```java
@Path("/users")  
@Produces(MediaType.APPLICATION_JSON)  
@Consumes(MediaType.APPLICATION_JSON)  
public class UserResource {  
  
    @GET  
    @Path("/{id}")  
    public Response getUser(@PathParam("id") Long id) {  
        User user = userService.findById(id);  
        return Response.ok(user).build();  
    }  
  
    @POST  
    public Response createUser(User user) {  
        User created = userService.create(user);  
        URI uri = uriInfo.getAbsolutePathBuilder().path("{id}")  
            .build(created.getId());  
        return Response.created(uri).entity(created).build();  
    }  
}
```

These were some tips and examples to create a REST API in the right way.

We have discussed some principles nd frameworks used to create REST APIs in Java. **_Please share your thoughts if you need more information on any of the mentioned topics. I will be happy to cover them in future articles._**

## Here are More Contents You Might Like

[

## You Must Know These 8 Performance Tuning Tips for Java Applications

### Java has become faster and more secure over time. With numerous improvements in garbage collectors, virtual threads…

medium.com



](https://medium.com/javarevisited/you-must-know-these-8-performance-tuning-tips-for-java-applications-51d433353efb?source=post_page-----dc2c3e263004---------------------------------------)

[

## Understanding MCP and How to Create a MCP Server using Spring Boot?

### MCP stands for Model Context Protocol. It is an open-source standard which was introduced in November 2024 by…

medium.com



](https://medium.com/javarevisited/understanding-mcp-and-how-to-create-a-mcp-server-using-spring-boot-5e43a87145a7?source=post_page-----dc2c3e263004---------------------------------------)

> _Thanks for reading. If you enjoy my content and want to show support, you can check out my other articles, give a_ **_few claps, and follow me_** _for more such content. I write on Java, Productivity, and Technology._

**_Until Next Time  
Saquib Aftab_**

[

Rest Api

](https://medium.com/tag/rest-api?source=post_page-----dc2c3e263004---------------------------------------)

[

Spring Boot

](https://medium.com/tag/spring-boot?source=post_page-----dc2c3e263004---------------------------------------)

[

Software Development

](https://medium.com/tag/software-development?source=post_page-----dc2c3e263004---------------------------------------)

[

Programming

](https://medium.com/tag/programming?source=post_page-----dc2c3e263004---------------------------------------)

[

Technology

](https://medium.com/tag/technology?source=post_page-----dc2c3e263004---------------------------------------)

148

1

[

![Javarevisited](https://miro.medium.com/v2/resize:fill:96:96/1*ceHirGi683U9Xn6tAlr0jQ.jpeg)



](https://medium.com/javarevisited?source=post_page---post_publication_info--dc2c3e263004---------------------------------------)

[

## Published in Javarevisited

](https://medium.com/javarevisited?source=post_page---post_publication_info--dc2c3e263004---------------------------------------)

[51K followers](https://medium.com/javarevisited/followers?source=post_page---post_publication_info--dc2c3e263004---------------------------------------)

·[Last published 7 hours ago](https://medium.com/javarevisited/i-tried-30-golang-courses-here-are-my-top-5-recommendations-for-2026-14cc90c06165?source=post_page---post_publication_info--dc2c3e263004---------------------------------------)

A humble place to learn Java and Programming better.

Following

[

![Saquib Aftab](https://miro.medium.com/v2/resize:fill:96:96/1*4FBZGZZMC_iScexvKZ7x5w.jpeg)



](https://medium.com/@saquibdev?source=post_page---post_author_info--dc2c3e263004---------------------------------------)

[

## Written by Saquib Aftab

](https://medium.com/@saquibdev?source=post_page---post_author_info--dc2c3e263004---------------------------------------)

[796 followers](https://medium.com/@saquibdev/followers?source=post_page---post_author_info--dc2c3e263004---------------------------------------)

·[40 following](https://medium.com/@saquibdev/following?source=post_page---post_author_info--dc2c3e263004---------------------------------------)

Lead Software Engineer | Java | Technology | Productivity | Learning and Improving to become a better developer

Following

## Responses (1)

[](https://policy.medium.com/medium-rules-30e5502c4eb4?source=post_page---post_responses--dc2c3e263004---------------------------------------)

![Tlamatini Israel](https://miro.medium.com/v2/resize:fill:64:64/0*bV-iWgTgY7UvX2Re)

Tlamatini Israel

﻿

Cancel

Respond

[

![Greg Hall](https://miro.medium.com/v2/resize:fill:64:64/0*tnPH-SoNrDEVxcit)



](https://medium.com/@greghall_29973?source=post_page---post_responses--dc2c3e263004----0-----------------------------------)

[

Greg Hall



](https://medium.com/@greghall_29973?source=post_page---post_responses--dc2c3e263004----0-----------------------------------)

[

Oct 14

](https://medium.com/@greghall_29973/for-apis-that-are-always-internal-you-dont-need-versioning-cause-only-you-will-use-them-9f75cddeb90d?source=post_page---post_responses--dc2c3e263004----0-----------------------------------)

For APIs that are always internal, you don't need versioning, cause only you will use them.

You don't have to separate create from update, this is a fake distinction that offers zero value.

Most of the time, the data is going into an SQL database, and…more

1 reply

Reply

## [More from the list: "Reading list"](https://medium.com/@isra.dgr.2020/list/reading-list?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

Curated by

[

Tlamatini Israel

](https://medium.com/@isra.dgr.2020?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

![Nnamdi Michael Okpala](https://miro.medium.com/v2/resize:fill:40:40/1*Tfe-449XsPtXTpvKnDa0iA.png)



](https://medium.com/@obinexus?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

Nnamdi Michael Okpala

](https://medium.com/@obinexus?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## I Built a Banking System That Talks COBOL… and My Boss Didn’t Notice

](https://medium.com/@obinexus/i-built-a-banking-system-that-talks-cobol-and-my-boss-didnt-notice-13b595b310ee?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

Sep 10

[

![Java Tips and Tricks](https://miro.medium.com/v2/resize:fill:40:40/1*Rdk8YkuDAjrr-yV3oUhL-g.jpeg)



](https://medium.com/java-tips-and-tricks?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

In

[

Java Tips and Tricks

](https://medium.com/java-tips-and-tricks?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

by

[

Renan Schmitt

](https://medium.com/@renanschmitt?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## Injection Framework for Non-Spring Projects

](https://medium.com/java-tips-and-tricks/injection-framework-for-non-spring-projects-99df7e25bd56?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

·

Jun 8

[

![Java Tips and Tricks](https://miro.medium.com/v2/resize:fill:40:40/1*Rdk8YkuDAjrr-yV3oUhL-g.jpeg)



](https://medium.com/java-tips-and-tricks?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

In

[

Java Tips and Tricks

](https://medium.com/java-tips-and-tricks?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

by

[

Renan Schmitt

](https://medium.com/@renanschmitt?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## JOOQ + Flux: An Efficient Way to Manage Memory Usage

](https://medium.com/java-tips-and-tricks/jooq-flux-an-efficient-way-to-manage-memory-usage-92fcdb2342fb?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

·

Dec 15, 2024

[

![Java Tips and Tricks](https://miro.medium.com/v2/resize:fill:40:40/1*Rdk8YkuDAjrr-yV3oUhL-g.jpeg)



](https://medium.com/java-tips-and-tricks?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

In

[

Java Tips and Tricks

](https://medium.com/java-tips-and-tricks?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

by

[

Renan Schmitt

](https://medium.com/@renanschmitt?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## How to Effectively Process a 10GB File

](https://medium.com/java-tips-and-tricks/how-to-effectively-process-a-10gb-file-2d990e991825?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

·

Jan 4

[

![Java Tips and Tricks](https://miro.medium.com/v2/resize:fill:40:40/1*Rdk8YkuDAjrr-yV3oUhL-g.jpeg)



](https://medium.com/java-tips-and-tricks?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

In

[

Java Tips and Tricks

](https://medium.com/java-tips-and-tricks?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

by

[

Renan Schmitt

](https://medium.com/@renanschmitt?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## 🧠 Mastering Java the Smart Way: Tools, Tricks & Real-World Tactics

](https://medium.com/java-tips-and-tricks/mastering-java-the-smart-way-tools-tricks-real-world-tactics-7e02dfa36334?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

Aug 6

[

![ILLUMINATION](https://miro.medium.com/v2/resize:fill:40:40/1*AZxiin1Cvws3J0TwNUP2sQ.png)



](https://medium.com/illumination?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

In

[

ILLUMINATION

](https://medium.com/illumination?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

by

[

Gopi C K

](https://medium.com/@gopi_ck?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## 10 Ways to Earn Money in 2025 as a Developer

](https://medium.com/illumination/10-ways-to-earn-money-in-2025-as-a-developer-df0a7077a415?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

·

Nov 17

[

![Gaddam.Naveen](https://miro.medium.com/v2/resize:fill:40:40/1*ZE-CfQ1ViASqLlwps6EiBg.jpeg)



](https://medium.com/@gaddamnaveen192?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

Gaddam.Naveen

](https://medium.com/@gaddamnaveen192?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## I Asked ChatGPT to Be My Java Mentor — These 30 Prompts Changed Everything

](https://medium.com/@gaddamnaveen192/i-asked-chatgpt-to-be-my-java-mentor-these-30-prompts-changed-everything-44ac95b12dbc?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

·

Oct 25

[

![Level Up Coding](https://miro.medium.com/v2/resize:fill:40:40/1*5D9oYBd58pyjMkV_5-zXXQ.jpeg)



](https://medium.com/gitconnected?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

In

[

Level Up Coding

](https://medium.com/gitconnected?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

by

[

Kuldeep Yadav

](https://medium.com/@kuldeepyadavky?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## Oh My Zsh Tutorial: Make Your Terminal Work for You (Complete Guide)

](https://medium.com/gitconnected/oh-my-zsh-tutorial-make-your-terminal-work-for-you-complete-guide-e2a5d76ab467?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

Nov 23

[

![Somendradev](https://miro.medium.com/v2/resize:fill:40:40/0*RkkEgR4gAkcKK0X3)



](https://medium.com/@somendradev23?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

Somendradev

](https://medium.com/@somendradev23?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## 15 Small Dev Tools That Quietly Do the Work of Big Expensive Software

](https://medium.com/@somendradev23/15-small-dev-tools-that-quietly-do-the-work-of-big-expensive-software-c31592062b70?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

·

5d ago

[

![Mr.PlanB](https://miro.medium.com/v2/resize:fill:40:40/1*CJrpoiFAVfg2M1kwNyX3Xw.png)



](https://medium.com/@PlanB.?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

Mr.PlanB

](https://medium.com/@PlanB.?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

[

## Proxmox VE 9.1 is Here: What’s New, What’s Better, and What to Watch Out For

](https://medium.com/@PlanB./proxmox-ve-9-1-is-here-whats-new-what-s-better-and-what-to-watch-out-for-c1a44db6400c?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

·

Nov 19

[View list](https://medium.com/@isra.dgr.2020/list/reading-list?source=post_page---list_recirc--dc2c3e263004-----------predefined%3A2dddd5d08c01%3AREADING_LIST----------------------------)

## More from Saquib Aftab and Javarevisited

![Spring Boot Annotations Developers Often Use Incorrectly](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*0VGcQDMzB3d99-O0WnPcsg.png)

[

![Javarevisited](https://miro.medium.com/v2/resize:fill:40:40/1*ceHirGi683U9Xn6tAlr0jQ.jpeg)



](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004----0---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

In

[

Javarevisited

](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004----0---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

by

[

Saquib Aftab

](https://medium.com/@saquibdev?source=post_page---author_recirc--dc2c3e263004----0---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

[

## Spring Boot Annotations Developers Often Use Incorrectly

### For Java developers, Spring Boot is the go-to framework for building modern, scalable applications.



](https://medium.com/javarevisited/spring-boot-annotations-developers-often-use-incorrectly-75e429ab9984?source=post_page---author_recirc--dc2c3e263004----0---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

4d ago

[

5







](https://medium.com/javarevisited/spring-boot-annotations-developers-often-use-incorrectly-75e429ab9984?source=post_page---author_recirc--dc2c3e263004----0---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

![Oracle Java Interview Experience (2025) — 4 Rounds of Pure Stress](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*m-7-g67ZayU7DTOfxW3Cvg.png)

[

![Javarevisited](https://miro.medium.com/v2/resize:fill:40:40/1*ceHirGi683U9Xn6tAlr0jQ.jpeg)



](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004----1---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

In

[

Javarevisited

](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004----1---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

by

[

Harry

](https://medium.com/@harrymarkjava?source=post_page---author_recirc--dc2c3e263004----1---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

[

## Oracle Java Interview Experience (2025) — 4 Rounds of Pure Stress

### When Java Meets Stress: Oracle Edition



](https://medium.com/javarevisited/oracle-java-interview-experience-2025-4-rounds-of-pure-stress-3d4af45e4f45?source=post_page---author_recirc--dc2c3e263004----1---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

Aug 27

[

32

6







](https://medium.com/javarevisited/oracle-java-interview-experience-2025-4-rounds-of-pure-stress-3d4af45e4f45?source=post_page---author_recirc--dc2c3e263004----1---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

![15 Spring Boot Questions That Every Senior Developer Should Be Ready For](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*iuelzCT9WlqqLH5WlVb9FQ.png)

[

![Javarevisited](https://miro.medium.com/v2/resize:fill:40:40/1*ceHirGi683U9Xn6tAlr0jQ.jpeg)



](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004----2---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

In

[

Javarevisited

](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004----2---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

by

[

Harry

](https://medium.com/@harrymarkjava?source=post_page---author_recirc--dc2c3e263004----2---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

[

## 15 Spring Boot Questions That Every Senior Developer Should Be Ready For

### Stop Googling the Basics. If You Call Yourself Senior, Prove It



](https://medium.com/javarevisited/15-spring-boot-questions-that-every-senior-developer-should-be-ready-for-e319485aab25?source=post_page---author_recirc--dc2c3e263004----2---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

Sep 8

[

49







](https://medium.com/javarevisited/15-spring-boot-questions-that-every-senior-developer-should-be-ready-for-e319485aab25?source=post_page---author_recirc--dc2c3e263004----2---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

![How to Integrate Google Gemini AI to Spring Boot using API Keys?](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*9xPazH8cl4HN7O4L-xzjqg.png)

[

![Javarevisited](https://miro.medium.com/v2/resize:fill:40:40/1*ceHirGi683U9Xn6tAlr0jQ.jpeg)



](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004----3---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

In

[

Javarevisited

](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004----3---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

by

[

Saquib Aftab

](https://medium.com/@saquibdev?source=post_page---author_recirc--dc2c3e263004----3---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

[

## How to Integrate Google Gemini AI to Spring Boot using API Keys?

### AI has now become a core part of how modern systems think, respond and interact with users. Now, integrating Google’s Gemini Models into a…



](https://medium.com/javarevisited/how-to-integrate-google-gemini-ai-to-spring-boot-using-api-keys-7ecc4afd7e04?source=post_page---author_recirc--dc2c3e263004----3---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

Nov 27

[

58







](https://medium.com/javarevisited/how-to-integrate-google-gemini-ai-to-spring-boot-using-api-keys-7ecc4afd7e04?source=post_page---author_recirc--dc2c3e263004----3---------------------a7b9f359_19e6_43db_8115_c865e0e6a0c5--------------)

[

See all from Saquib Aftab

](https://medium.com/@saquibdev?source=post_page---author_recirc--dc2c3e263004---------------------------------------)

[

See all from Javarevisited

](https://medium.com/javarevisited?source=post_page---author_recirc--dc2c3e263004---------------------------------------)

## Recommended from Medium

![Stop Everything: Java 25 Just Introduced the Most Important Feature Since Java 8](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*AZkZ3uzl4_cpEk3wZgxZnA.png)

[

![Dilip Kumar – Build Catalyst](https://miro.medium.com/v2/resize:fill:40:40/1*_NhC1RP-rf17-ISphHJMxg.png)



](https://medium.com/@BuildCatalyst?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

Dilip Kumar – Build Catalyst

](https://medium.com/@BuildCatalyst?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

## Stop Everything: Java 25 Just Introduced the Most Important Feature Since Java 8

### A simple breakdown of Java 25’s biggest changes — and the new concurrency model every developer should understand.



](https://medium.com/@BuildCatalyst/stop-everything-java-25-just-introduced-the-most-important-feature-since-java-8-64e698a0bc57?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

6d ago

[

53

9







](https://medium.com/@BuildCatalyst/stop-everything-java-25-just-introduced-the-most-important-feature-since-java-8-64e698a0bc57?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

![8 Essential Data Structures Every Developer Should Know](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*X7nnCaBvD_wS9OjyFWvfdQ.png)

[

![Code With Sunil | Code Smarter, not harder](https://miro.medium.com/v2/resize:fill:40:40/1*-lvt1bxU-jKgyYc2iDJ-Vg.jpeg)



](https://medium.com/@sunil17bbmp?source=post_page---read_next_recirc--dc2c3e263004----1---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

Code With Sunil | Code Smarter, not harder

](https://medium.com/@sunil17bbmp?source=post_page---read_next_recirc--dc2c3e263004----1---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

## 8 Essential Data Structures Every Developer Should Know

### Learn the most important data structures used in real-world coding, interviews, and scalable applications.



](https://medium.com/@sunil17bbmp/8-essential-data-structures-every-developer-should-know-c09140fc9128?source=post_page---read_next_recirc--dc2c3e263004----1---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

Nov 6

[

88







](https://medium.com/@sunil17bbmp/8-essential-data-structures-every-developer-should-know-c09140fc9128?source=post_page---read_next_recirc--dc2c3e263004----1---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

![7 Spring Boot Patterns That Will Dominate Microservices in 2026](https://miro.medium.com/v2/resize:fit:1358/format:webp/0*0wAWV1v0MSCebr6q)

[

![JavaScript in Plain English](https://miro.medium.com/v2/resize:fill:40:40/1*yUNfohs9jA6GCDmyCYJTvA@2x.png)



](https://medium.com/javascript-in-plain-english?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

In

[

JavaScript in Plain English

](https://medium.com/javascript-in-plain-english?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

by

[

Riya Sharma

](https://medium.com/@csvaibhavpasrija?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

## 7 Spring Boot Patterns That Will Dominate Microservices in 2026

### The Blueprint Every Modern Java Developer Must Master



](https://medium.com/javascript-in-plain-english/7-spring-boot-patterns-that-will-dominate-microservices-in-2026-d08425c04d01?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

Dec 3

[

32







](https://medium.com/javascript-in-plain-english/7-spring-boot-patterns-that-will-dominate-microservices-in-2026-d08425c04d01?source=post_page---read_next_recirc--dc2c3e263004----0---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

![15 Bad Programming Habits You Need to Stop Now (so you actually become a better developer)](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*maWZ70-p-7AOzoN5kb04TQ.png)

[

![Usman Writes](https://miro.medium.com/v2/resize:fill:40:40/1*bMbe1ORqVNQs2qCyBZSJEQ.png)



](https://medium.com/@pixicstudio?source=post_page---read_next_recirc--dc2c3e263004----1---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

Usman Writes

](https://medium.com/@pixicstudio?source=post_page---read_next_recirc--dc2c3e263004----1---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

## 15 Bad Programming Habits You Need to Stop Now (so you actually become a better developer)

### You want to be better. Good. That means cutting the bulls*** and fixing the habits that make you unreliable, slow, and annoying to work…



](https://medium.com/@pixicstudio/15-bad-programming-habits-you-need-to-stop-now-so-you-actually-become-a-better-developer-c7d8dfceae89?source=post_page---read_next_recirc--dc2c3e263004----1---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

Nov 20

[

212

5







](https://medium.com/@pixicstudio/15-bad-programming-habits-you-need-to-stop-now-so-you-actually-become-a-better-developer-c7d8dfceae89?source=post_page---read_next_recirc--dc2c3e263004----1---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

![My 5 Years of Java Experience Collapsed in a 60-Minute Interview](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*V_E4IH5Alduu3N49dDVGEQ.png)

[

![Stackademic](https://miro.medium.com/v2/resize:fill:40:40/1*U-kjsW7IZUobnoy1gAp1UQ.png)



](https://medium.com/stackademic?source=post_page---read_next_recirc--dc2c3e263004----2---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

In

[

Stackademic

](https://medium.com/stackademic?source=post_page---read_next_recirc--dc2c3e263004----2---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

by

[

Shanu Reddy

](https://medium.com/@shanureddy?source=post_page---read_next_recirc--dc2c3e263004----2---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

## My 5 Years of Java Experience Collapsed in a 60-Minute Interview

### 👉If you are not a Member — Read for free here :



](https://medium.com/stackademic/my-5-years-of-java-experience-collapsed-in-a-60-minute-interview-34d8657eba66?source=post_page---read_next_recirc--dc2c3e263004----2---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

Dec 3

[

17

1







](https://medium.com/stackademic/my-5-years-of-java-experience-collapsed-in-a-60-minute-interview-34d8657eba66?source=post_page---read_next_recirc--dc2c3e263004----2---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

![20 Software Architectures Every Java Developer Must Know Before 2026](https://miro.medium.com/v2/resize:fit:1358/format:webp/1*bRMHXtIugwqiVRRQUxN9Pg.png)

[

![Gaddam.Naveen](https://miro.medium.com/v2/resize:fill:40:40/1*ZE-CfQ1ViASqLlwps6EiBg.jpeg)



](https://medium.com/@gaddamnaveen192?source=post_page---read_next_recirc--dc2c3e263004----3---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

Gaddam.Naveen

](https://medium.com/@gaddamnaveen192?source=post_page---read_next_recirc--dc2c3e263004----3---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

## 20 Software Architectures Every Java Developer Must Know Before 2026

### if you are not a medium member then Click here to read free



](https://medium.com/@gaddamnaveen192/20-software-architectures-every-java-developer-must-know-before-2026-97865e3f21c4?source=post_page---read_next_recirc--dc2c3e263004----3---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

Dec 1

[

121

1







](https://medium.com/@gaddamnaveen192/20-software-architectures-every-java-developer-must-know-before-2026-97865e3f21c4?source=post_page---read_next_recirc--dc2c3e263004----3---------------------92766fab_3bf9_4a77_8b32_87f828dda855--------------)

[

See more recommendations

](https://medium.com/?source=post_page---read_next_recirc--dc2c3e263004---------------------------------------)

[

Help

](https://help.medium.com/hc/en-us?source=post_page-----dc2c3e263004---------------------------------------)

[

Status

](https://status.medium.com/?source=post_page-----dc2c3e263004---------------------------------------)

[

About

](https://medium.com/about?autoplay=1&source=post_page-----dc2c3e263004---------------------------------------)

[

Careers

](https://medium.com/jobs-at-medium/work-at-medium-959d1a85284e?source=post_page-----dc2c3e263004---------------------------------------)

[

Press

](mailto:pressinquiries@medium.com)

[

Blog

](https://blog.medium.com/?source=post_page-----dc2c3e263004---------------------------------------)

[

Privacy

](https://policy.medium.com/medium-privacy-policy-f03bf92035c9?source=post_page-----dc2c3e263004---------------------------------------)

[

Rules

](https://policy.medium.com/medium-rules-30e5502c4eb4?source=post_page-----dc2c3e263004---------------------------------------)

[

Terms

](https://policy.medium.com/medium-terms-of-service-9db0094a1e0f?source=post_page-----dc2c3e263004---------------------------------------)

[

Text to speech

](https://speechify.com/medium?source=post_page-----dc2c3e263004---------------------------------------)